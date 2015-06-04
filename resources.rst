Resources
=========

Conventions
-----------

Date / Time
^^^^^^^^^^^

We use a simplified version of the ISO 8601 standard. Dates are represented in the ``YYYY-MM-DD`` 
format. Most dates with timestamps follow the ``YYYY-MM-DD hh:mm:ss`` format (e.g. "2015-03-15 19:33:59"), 
where the timestamp is "timezoneless", it is implied to be in your organization's timezone 
(or the timezone is irrelevant).

For a few timestamp type data fields, we use the (still ISO 8601 standard) ``YYYY-MM-DDThh:mm:ss+00:00`` 
format (example: "2015-03-21T19:45:33-06:00"). This is used for fields like contact time, response time, 
creation date etc., where the timezone may be important (due to daylight savings time for example).

.. rubric:: Endpoints

``/schedule``
----------------

The schedule consists of *shifts*, *time offs*, *callbacks*, *trades* and *misc. hours*.
They are wrapped by a top-level object containing metadata about the requested schedule (start date, end date, links for the next and previous period).

This endpoint has two required parameters:

+----------------+-----------------------------+-------------------------+
| Parameter      | Description                 | Format                  |
+================+=============================+=========================+
| ``start``      | The date                    | datetime                |
|                | you need the data from      |                         |
+----------------+-----------------------------+-------------------------+
| ``end``        | The date                    | datetime                |
|                | you need the data to        |                         |
+----------------+-----------------------------+-------------------------+

An example of returned schedule data looks like this::

   {
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-22 08:00:00",

      "days": [
         {
            "date": "2015-03-15",
            "assignments": [
               {
                  "shifts": [
                     ... shift data ...
                  ],
               }
            ],
            "time_off": [
               {
                  ... time off data ...
               }
            ],
            "callbacks": [
               {
                  ... callback data ...
               }
            ],
            "trades": [
               {
                  ... trade data ...
               }
            ],
            "misc": [
               {
                  ... misc. hours data ...
               }
            ]
         }
      ],

      "prev": {
         "href": "https://callbackstaffing.com/api/v1/schedule?start=2015-03-08%2008:00:00&end=2015-03-15%2008:00:00"
      },
      "next": {
         "href": "https://callbackstaffing.com/api/v1/schedule?start=2015-03-22%2008:00:00&end=2015-03-22%2008:00:00"
      }
   }

.. note::

   While we are trying to make the API *RESTful*, some resources, including this one, are more of 
   a convenient packaging of multiple resources for querying. You cannot issue a ``POST`` or ``DELETE``
   request on this endpoint. 

In the following sections, we try to introduce all the important data in the ``schedule`` resource.

``days``
^^^^^^^^

This array contains all days occurring between the start and end date requested. Each object in the array contains the ``date`` 
key, and arrays of objects occurring that day. For the following sections, we refer to one object in this array as a ``day``.

``day.assignments``
^^^^^^^^^^^^^^^^^^^

This array contains the assignments of the day. An assignment looks like this::

   {
      "id": 1234,
      "href": "https://callbackstaffing.com/api/v1/assignments/1234",
      "date": "2015-03-15",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "name": "Station 1",
      "positions_to_fill": 3,
      "shifts": [
         ... see next section ...
      ]
   }

+-----------------------+---------------------------+--------------------+
| Field                 | Description               | Type               |
+=======================+===========================+====================+
| ``id``                | Unique identifier of the  | integer            |
|                       | assignment                |                    |
+-----------------------+---------------------------+--------------------+
| ``href``              | Link to full object       | string (URL)       |
+-----------------------+---------------------------+--------------------+
| ``date``              | The day the assignment    | date               |
|                       | starts on                 |                    |
+-----------------------+---------------------------+--------------------+
| ``start``             | Start date of assignment  | datetime           |
+-----------------------+---------------------------+--------------------+
| ``end``               | End date of assignment    | datetime           |
+-----------------------+---------------------------+--------------------+
| ``name``              | Title of assignment       | string             |
+-----------------------+---------------------------+--------------------+
| ``positions_to_fill`` | Employees needed          | integer            |
+-----------------------+---------------------------+--------------------+
| ``shifts``            | Employees working the     | array              |
|                       | assignment                |                    |
+-----------------------+---------------------------+--------------------+

``day.assignment.shifts``
^^^^^^^^^^^^^^^^^^^^^^^^^^

This array holds data about the employees scheduled for the assignment on the given day. An object of this array is formatted 
like this::

   {
      "id": 456789,
      "href": "https://callbackstaffing.com/api/v1/shifts/456789",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "hold_over": 0,
      "recurring": true,
      "user": {
         "id": 848,
         "href": "https://callbackstaffing.com/api/v1/users/848",
         "name": "John Doe"
      },
      "admin": {
         "id": 138,
         "href": "https://callbackstaffing.com/api/v1/users/138",
         "name": "Joe Boss"
      },
      "work_type": {
         "id": 33,
         "href": "https://callbackstaffing.com/api/v1/work_types/33",
         "name": "Regular Time",
         "work_code": "REG001"
      },
      "labels": [
         {
            "id": 12,
            "href": "https://callbackstaffing.com/api/v1/labels/12",
            "label": "ENG"
         }
      ]
   }

+-----------------------+---------------------------+--------------------+
| Field                 | Description               | Type               |
+=======================+===========================+====================+
| ``id``                | Unique identifier of the  | integer            |
|                       | work shift                |                    |
+-----------------------+---------------------------+--------------------+
| ``href``              | Link to full object       | string (URL)       |
+-----------------------+---------------------------+--------------------+
| ``start``             | Start date of shift       | datetime           |
+-----------------------+---------------------------+--------------------+
| ``end``               | End date of shift         | datetime           |
+-----------------------+---------------------------+--------------------+
| ``hold_over``         | Additional OT hours       | datetime           |
+-----------------------+---------------------------+--------------------+
| ``recurring``         | Is it a regularly         | boolean            |
|                       | occurring shift?          |                    |
+-----------------------+---------------------------+--------------------+
| ``user``              | Employee working the      |See                 |
|                       | shift                     |:ref:`section-users`|
+-----------------------+---------------------------+--------------------+
| ``admin``             | Admin who assigned the    |See                 |
|                       | shift                     |:ref:`section-users`|
+-----------------------+---------------------------+--------------------+
| ``work_type``         | Type of work              |See                 |
|                       | shift                     |:ref:`section-wt`   |
+-----------------------+---------------------------+--------------------+
| ``labels``            | Applied Crew Scheduler    |array; see          |
|                       | labels                    |:ref:`section-label`|
+-----------------------+---------------------------+--------------------+

You will notice that some of the included objects have ``href`` properties. This is because we are only returning a sensible 
subset of the available data about these objects. If you make a ``GET`` request to the provided URL, you can retrieve all of 
the available information about them.

``day.time_off``
^^^^^^^^^^^^^^^^

All approved time off for the day is in this array, including long term and recurring leave that has an occurrence fall on this 
day. The general structure of one object in the array::

   {
      "id": 623492,
      "href": "https://callbackstaffing.com/api/v1/time_off/623492",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "user": {
         "id": 848,
         "href": "https://callbackstaffing.com/api/v1/users/848",
         "name": "John Doe"
      },
      "admin": {
         "id": 138,
         "href": "https://callbackstaffing.com/api/v1/users/138",
         "name": "Joe Boss"
      },
      "time_off_type": {
         "id": 45,
         "href": "https://callbackstaffing.com/api/v1/time_off_types/45",
         "name": "Sick Leave [SL]"
      }
   }

+-----------------------+---------------------------+--------------------+
| Field                 | Description               | Type               |
+=======================+===========================+====================+
| ``id``                | Unique identifier of the  | integer            |
|                       | time off                  |                    |
+-----------------------+---------------------------+--------------------+
| ``href``              | Link to full object       | string (URL)       |
+-----------------------+---------------------------+--------------------+
| ``start``             | Start date of time off    | datetime           |
+-----------------------+---------------------------+--------------------+
| ``end``               | End date of time off      | datetime           |
+-----------------------+---------------------------+--------------------+
| ``user``              | Employee on leave         |See                 |
|                       |                           |:ref:`section-users`|
+-----------------------+---------------------------+--------------------+
| ``admin``             | Admin who approved the    |See                 |
|                       | time off                  |:ref:`section-users`|
+-----------------------+---------------------------+--------------------+
| ``time_off_type``     | Type of time off          |See                 |
|                       | shift                     |:ref:`section-tot`  |
+-----------------------+---------------------------+--------------------+

``day.callbacks``
^^^^^^^^^^^^^^^^^

In this array you will find all finalized callbacks for the day. Callback shifts that were drag & dropped to a work assignment 
will not be included, they are under ``day.assignment.shifts``. A ``callback`` object is structured like this::

   {
      "id": 64012,
      "href": "https://callbackstaffing.com/api/v1/callbacks/64012",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "positions_to_fill": 1,
      "records": [
         {
            "id": 2165743,
            "user": {
               "id": 848,
               "href": "https://callbackstaffing.com/api/v1/users/848",
               "name": "John Doe"
            },
            "start": "2015-03-15 08:00:00",
            "end": "2015-03-16 08:00:00",
            "work_site": null
         }
      ]
      "title": {
         "id": 112,
         "href": "https://callbackstaffing.com/api/v1/titles/112",
         "name": "Firefighter"
      }
   }

+-----------------------+---------------------------+--------------------+
| Field                 | Description               | Type               |
+=======================+===========================+====================+
| ``id``                | Unique identifier of the  | integer            |
|                       | time off                  |                    |
+-----------------------+---------------------------+--------------------+
| ``href``              | Link to full object       | string (URL)       |
+-----------------------+---------------------------+--------------------+
| ``start``             | Start date of the         | datetime           |
|                       | callback shift            |                    |
+-----------------------+---------------------------+--------------------+
| ``end``               | End date of the           | datetime           |
|                       | callback shift            |                    |
+-----------------------+---------------------------+--------------------+
| ``positions_to_fill`` | Employees needed          | integer            |
+-----------------------+---------------------------+--------------------+
| ``records``           | Accepting employees       |array; see          |
|                       |                           |:ref:`section-cbr`  | 
+-----------------------+---------------------------+--------------------+
| ``title``             | Employee type needed      |See                 |
|                       | time off                  |:ref:`section-title`|
+-----------------------+---------------------------+--------------------+

.. note::

   ``records`` gives you all accepting employees of the callback. You can request more data about certain pieces of the callback 
   using the ``href`` links provided.

``day.trades``
^^^^^^^^^^^^^^^^

``trades`` contains all accepted and finalized shift trades for the day. A trade object in the array looks like this::

   {
      "id": 4355,
      "href": "https://callbackstaffing.com/api/v1/trades/4355",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "requesting_user": {
         "id": 848,
         "href": "https://callbackstaffing.com/api/v1/users/848",
         "name": "John Doe"
      },
      "accepting_user": {
         "id": 138,
         "href": "https://callbackstaffing.com/api/v1/users/138",
         "name": "Jack Smith"
      },
      "admin": {
         "id": 98,
         "href": "https://callbackstaffing.com/api/v1/users/98",
         "name": "Steve Boss"
      }
   }

Follow the top-level ``href`` link to receive all information about the trade.

``day.misc``
^^^^^^^^^^^^

This array provides data about any miscellaneous hours added for the day, in the following format::

   {
      "id": 47711,
      "href": "https://callbackstaffing.com/api/v1/misc/47711",
      "date": "2015-03-16",
      "length": 4.5,
      "user": {
         "id": 848,
         "href": "https://callbackstaffing.com/api/v1/users/848",
         "name": "John Doe"
      },
      "work_type": "Training"
   }

.. _section-tot:

``/time_off_types``
-------------------

Get all non-deleted time off types for the active company. Format::

   [ 
      {
         "id": "5",
         "label": "Sick",
         "work_code": "SL",
         "required_buffer": "0.00",
         "instance_limit": "1",
         "primary_color": "#2474a9",
         "secondary_color": "#FFFFFF",
         "force_include": true,
         "forward": false,
         "href": "https://callbackstaffing.com/api/v1/time_off_types/5"
      }
      {
         "id": "6",
         "label": "Vacation",
         "work_code": "VAC",
         "required_buffer": "0.00",
         "instance_limit": "0",
         "primary_color": "#3f5647",
         "secondary_color": "#FFFFFF",
         "force_include": false,
         "forward": true,
         "href": "https://callbackstaffing.com/api/v1/time_off_types/6"
      }
   ]

+-----------------------+---------------------------+--------------------+
| Field                 | Description               | Type               |
+=======================+===========================+====================+
| ``id``                | Unique identifier of the  | integer            |
|                       | time off type             |                    |
+-----------------------+---------------------------+--------------------+
| ``href``              | Link to full object       | string (URL)       |
+-----------------------+---------------------------+--------------------+
| ``label``             | Name of the               | string             |
|                       | time off type             |                    |
+-----------------------+---------------------------+--------------------+
| ``work_code``         | Shortcode of the          | string             |
|                       | time off type             |                    |
+-----------------------+---------------------------+--------------------+
| ``required_buffer``   | Hours needed between      | decimal            |
|                       | request and start of the  |                    |
|                       | time off entry            |                    |
+-----------------------+---------------------------+--------------------+
| ``instance_limit``    | Max. allowed number of    | integer            |
|                       | this type in a year       |                    | 
+-----------------------+---------------------------+--------------------+
| ``primary_color``     | Main color of the type    | hexadecimal        |
|                       | (background color)        | RGB code           |
+-----------------------+---------------------------+--------------------+
| ``secondary_color``   | Text color of the type    | hexadecimal        |
|                       |                           | RGB code           |
+-----------------------+---------------------------+--------------------+
| ``force_include``     | Ignore time off of this   | boolean            |
|                       | type in callbacks         |                    |
+-----------------------+---------------------------+--------------------+
| ``forward``           | Forward time off of this  | boolean            |
|                       | type to other admins      |                    |
|                       | if not handled            |                    |
+-----------------------+---------------------------+--------------------+

.. _section-users:

``/users``
----------

*Coming soon...*

.. _section-wt:

``/work_types``
---------------

*Coming soon...*

.. _section-label:

``/labels``
-------------------

*Coming soon...*

.. _section-cbr:

``/callback_records``
---------------------

*Coming soon...*

.. _section-title:

``/titles``
---------------------

*Coming soon...*