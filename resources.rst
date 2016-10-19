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

Schedule
--------

``GET /schedule``
^^^^^^^^^^^^^^^^^

The schedule consists of *shifts*, *time offs*, *callbacks*, *trades*, *notes*, *activities* and *misc. hours*.
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
         "href": "https://api.crewsense.com/v1/schedule?start=2015-03-08%2008:00:00&end=2015-03-15%2008:00:00"
      },
      "next": {
         "href": "https://api.crewsense.com/v1/schedule?start=2015-03-22%2008:00:00&end=2015-03-22%2008:00:00"
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
      "href": "https://api.crewsense.com/v1/assignments/1234",
      "date": "2015-03-15",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "name": "Station 1",
      "minimum_staffing": 3,
      "shifts": [
         ... see next section ...
      ]
   }

+----------------------+--------------------------+--------------+
| Field                | Description              | Type         |
+======================+==========================+==============+
| ``id``               | Unique identifier of the | integer      |
|                      | assignment               |              |
+----------------------+--------------------------+--------------+
| ``href``             | Link to full object      | string (URL) |
+----------------------+--------------------------+--------------+
| ``date``             | The day the assignment   | date         |
|                      | starts on                |              |
+----------------------+--------------------------+--------------+
| ``start``            | Start date of assignment | datetime     |
+----------------------+--------------------------+--------------+
| ``end``              | End date of assignment   | datetime     |
+----------------------+--------------------------+--------------+
| ``name``             | Title of assignment      | string       |
+----------------------+--------------------------+--------------+
| ``minimum_staffing`` | Employees needed         | integer      |
+----------------------+--------------------------+--------------+
| ``shifts``           | Employees working the    | array        |
|                      | assignment               |              |
+----------------------+--------------------------+--------------+

``day.assignment.shifts``
^^^^^^^^^^^^^^^^^^^^^^^^^^

This array holds data about the employees scheduled for the assignment on the given day. An object of this array is formatted 
like this::

   {
      "id": 456789,
      "href": "https://api.crewsense.com/v1/shifts/456789",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "hold_over": 0,
      "recurring": true,
      "user": {
         "id": 848,
         "href": "https://api.crewsense.com/v1/users/848",
         "name": "John Doe"
      },
      "scheduled_by": {
         "id": 138,
         "href": "https://api.crewsense.com/v1/users/138",
         "name": "Joe Boss"
      },
      "work_type": {
         "id": 33,
         "href": "https://api.crewsense.com/v1/work_types/33",
         "name": "Regular Time",
         "work_code": "REG001"
      },
      "labels": [
         {
            "id": 12,
            "href": "https://api.crewsense.com/v1/labels/12",
            "label": "ENG"
         }
      ]
   }

+------------------+--------------------------+----------------------+
| Field            | Description              | Type                 |
+==================+==========================+======================+
| ``id``           | Unique identifier of the | integer              |
|                  | work shift               |                      |
+------------------+--------------------------+----------------------+
| ``href``         | Link to full object      | string (URL)         |
+------------------+--------------------------+----------------------+
| ``start``        | Start date of shift      | datetime             |
+------------------+--------------------------+----------------------+
| ``end``          | End date of shift        | datetime             |
+------------------+--------------------------+----------------------+
| ``hold_over``    | Additional OT hours      | datetime             |
+------------------+--------------------------+----------------------+
| ``recurring``    | Is it a regularly        | boolean              |
|                  | occurring shift?         |                      |
+------------------+--------------------------+----------------------+
| ``user``         | Employee working the     | See                  |
|                  | shift                    | :ref:`section-users` |
+------------------+--------------------------+----------------------+
| ``scheduled_by`` | Admin who assigned the   | See                  |
|                  | shift                    | :ref:`section-users` |
+------------------+--------------------------+----------------------+
| ``work_type``    | Type of work             | See                  |
|                  | shift                    | :ref:`section-wt`    |
+------------------+--------------------------+----------------------+
| ``labels``       | Applied Crew Scheduler   | array; see           |
|                  | labels                   | :ref:`section-label` |
+------------------+--------------------------+----------------------+

You will notice that some of the included objects have ``href`` properties. This is because we are only returning a sensible 
subset of the available data about these objects. If you make a ``GET`` request to the provided URL, you can retrieve all of 
the available information about them.

``day.time_off``
^^^^^^^^^^^^^^^^

All approved time off for the day is in this array, including long term and recurring leave that has an occurrence fall on this 
day. The general structure of one object in the array::

   {
      "id": 623492,
      "href": "https://api.crewsense.com/v1/time_off/623492",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "user": {
         "id": 848,
         "href": "https://api.crewsense.com/v1/users/848",
         "name": "John Doe"
      },
      "admin": {
         "id": 138,
         "href": "https://api.crewsense.com/v1/users/138",
         "name": "Joe Boss"
      },
      "time_off_type": {
         "id": 45,
         "href": "https://api.crewsense.com/v1/time_off_types/45",
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
      "href": "https://api.crewsense.com/v1/callbacks/64012",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "minimum_staffing": 1,
      "records": [
         {
            "id": 2165743,
            "user": {
               "id": 848,
               "href": "https://api.crewsense.com/v1/users/848",
               "name": "John Doe"
            },
            "start": "2015-03-15 08:00:00",
            "end": "2015-03-16 08:00:00",
            "work_site": null
         }
      ]
      "title": {
         "id": 112,
         "href": "https://api.crewsense.com/v1/titles/112",
         "name": "Firefighter"
      }
   }

+----------------------+--------------------------+----------------------+
| Field                | Description              | Type                 |
+======================+==========================+======================+
| ``id``               | Unique identifier of the | integer              |
|                      | time off                 |                      |
+----------------------+--------------------------+----------------------+
| ``href``             | Link to full object      | string (URL)         |
+----------------------+--------------------------+----------------------+
| ``start``            | Start date of the        | datetime             |
|                      | callback shift           |                      |
+----------------------+--------------------------+----------------------+
| ``end``              | End date of the          | datetime             |
|                      | callback shift           |                      |
+----------------------+--------------------------+----------------------+
| ``minimum_staffing`` | Employees needed         | integer              |
+----------------------+--------------------------+----------------------+
| ``records``          | Accepting employees      | array; see           |
|                      |                          | :ref:`section-cbr`   |
+----------------------+--------------------------+----------------------+
| ``title``            | Employee type needed     | See                  |
|                      | time off                 | :ref:`section-title` |
+----------------------+--------------------------+----------------------+

.. note::

   ``records`` gives you all accepting employees of the callback. You can request more data about certain pieces of the callback 
   using the ``href`` links provided.

``day.trades``
^^^^^^^^^^^^^^^^

``trades`` contains all accepted and finalized shift trades for the day. A trade object in the array looks like this::

   {
      "id": 4355,
      "href": "https://api.crewsense.com/v1/trades/4355",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "requesting_user": {
         "id": 848,
         "href": "https://api.crewsense.com/v1/users/848",
         "name": "John Doe"
      },
      "accepting_user": {
         "id": 138,
         "href": "https://api.crewsense.com/v1/users/138",
         "name": "Jack Smith"
      },
      "admin": {
         "id": 98,
         "href": "https://api.crewsense.com/v1/users/98",
         "name": "Steve Boss"
      }
   }

Follow the top-level ``href`` link to receive all information about the trade.

``day.misc``
^^^^^^^^^^^^

This array provides data about any miscellaneous hours added for the day, in the following format::

   {
      "id": 47711,
      "href": "https://api.crewsense.com/v1/misc/47711",
      "date": "2015-03-16",
      "length": 4.5,
      "user": {
         "id": 848,
         "href": "https://api.crewsense.com/v1/users/848",
         "name": "John Doe"
      },
      "work_type": "Training"
   }

``day.notes``, ``day.activities``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This contains the Crew Scheduler notes for the day, with all the HTML from the Rich Text Editor::

   "notes": "<p>These are your notes for the day.<br />Notice the HTML</p>"
   "activities": "<h1>TRAINING</h1><p>These are your activities.<br />In HTML too!</p>"

.. _section-tot:

Time Off Types
--------------

``GET /time_off_types``
^^^^^^^^^^^^^^^^^^^^^^^

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
         "href": "https://api.crewsense.com/v1/time_off_types/5"
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
         "href": "https://api.crewsense.com/v1/time_off_types/6"
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
| ``primary_color``     | Main color of the type    | RGB hex            |
|                       | (background color)        |                    |
+-----------------------+---------------------------+--------------------+
| ``secondary_color``   | Text color of the type    | RGB hex            |
|                       |                           |                    |
+-----------------------+---------------------------+--------------------+
| ``force_include``     | Ignore time off of this   | boolean            |
|                       | type in callbacks         |                    |
+-----------------------+---------------------------+--------------------+
| ``forward``           | Forward time off of this  | boolean            |
|                       | type to other admins      |                    |
|                       | if not handled            |                    |
+-----------------------+---------------------------+--------------------+

.. _section-label:

Labels
------

Manage crew scheduler labels with these endpoints.

+----------------+---------------------------------------------+---------+
| Field          | Description                                 | Type    |
+================+=============================================+=========+
| ``id``         | Unique identifier of the label              | integer |
+----------------+---------------------------------------------+---------+
| ``label``      | The text appearing on the label             | string  |
+----------------+---------------------------------------------+---------+
| ``color``      | The background color of the label           | RGB hex |
+----------------+---------------------------------------------+---------+
| ``text_color`` | The text color of the label                 | RGB hex |
+----------------+---------------------------------------------+---------+
| ``position``   | Relative position of shifts with this label | integer |
+----------------+---------------------------------------------+---------+

``GET /labels``
^^^^^^^^^^^^^^^

Receive a list of all crew scheduler labels available for the company.
Example response::

   [ 
      {
         "id": "1773",
         "label": "CPT",
         "color": "#CCCCCC",
         "text_color": "#333333",
         "position": "1"
      },
      {
         "id": "1774",
         "label": "ENG",
         "color": "#ff0000",
         "text_color": "#ffffff",
         "position": "2"
      }
   ]




``GET /labels/{id}``
^^^^^^^^^^^^^^^^^^^^

Receive the details of one particular label.
Example response (``GET /labels/1773``)::

   {
      "id": "1773",
      "label": "CPT",
      "color": "#CCCCCC",
      "text_color": "#333333",
      "position": "1"
   }

``POST /labels``
^^^^^^^^^^^^^^^^

Create a new crew scheduler label in the system.
Required fields:
   
   * ``label`` - the text on the label
   * ``color`` - the background color of the label, in HEX format (#RRGGBB)
   * ``text_color`` - the text color of the label, in HEX format

Optional fields:

   * ``position`` - The relative position of shifts with this label inside an assignment


``POST /labels/{id}``
^^^^^^^^^^^^^^^^^^^^^

Change an existing crew scheduler label in the system.
Required fields:
   
   * ``label`` - the text on the label
   * ``color`` - the background color of the label, in HEX format (#RRGGBB)
   * ``text_color`` - the text color of the label, in HEX format

Optional fields:

   * ``position`` - The relative position of shifts with this label inside an assignment


``DELETE /labels/{id}``
^^^^^^^^^^^^^^^^^^^^^^^

Remove an existing crew scheduler label from the system.

.. _section-filter:

Filters
-------

Manage specialty classification filters

+----------------+------------------------------------------+-----------+
| Field          | Description                              | Type      |
+================+==========================================+===========+
| ``id``         | Unique identifier of the filter          | integer   |
+----------------+------------------------------------------+-----------+
| ``label``      | The name of the filter                   | string    |
+----------------+------------------------------------------+-----------+
| ``created_on`` | Timestamp of the creation of this filter | timestamp |
+----------------+------------------------------------------+-----------+
| ``user``       | The user who created this resource       | ``User``  |
+----------------+------------------------------------------+-----------+

``GET /filters``
^^^^^^^^^^^^^^^^

Receive a list of all active specialty classification filters
Example response::

   [ 
      {
         "id": "7",
         "label": "Rescue Certified",
         "created_on": "2014-10-29T02:17:51-0700",
         "user": {
            id: "848",
            name: "John Doe"
         }
      },
      {
         "id": "8",
         "label": "Dive Team",
         "created_on": "2014-10-30T12:04:01-0700",
         "user": {
            id: "848",
            name: "John Doe"
         }
      }
   ]




``GET /filters/{id}``
^^^^^^^^^^^^^^^^^^^^^

Receive the details of one particular specialty classification filter.
Example response (``GET /labels/7``)::

   {
      "id": "7",
         "label": "Rescue Certified",
         "created_on": "2014-10-29T02:17:51-0700",
         "deleted": "0",
         "user": {
            id: "848",
            name: "John Doe"
         }
   }

The ``deleted`` key indicates if the filter has been deleted, 0 - active, 1 - deleted. 

``POST /filters``
^^^^^^^^^^^^^^^^^

Create a new specialty classification filter in the system.
Required fields:
   
   * ``label`` - the name of the specialty classification filter


``POST /filters/{id}``
^^^^^^^^^^^^^^^^^^^^^^

Change an existing specialty classification filter in the system.
Required fields:
   
   * ``label`` - the name of the specialty classification filter


``DELETE /filters/{id}``
^^^^^^^^^^^^^^^^^^^^^^^^

Remove an existing specialty classification filter from the system.


.. _section-users:

Users
-----

``GET /users``
^^^^^^^^^^^^^^

List all non-deleted, active users of the company.

::

   [
      {
         "user_id": 1234,
         "employee_id": "DEV123",
         "username": "olinagy",
         "first_name": "Oliver",
         "last_name": "Nagy",
         "full_name": "Oliver Nagy",
         "role": "Deputy",
         "emails": [
            "oli.nagy@example.com",
            "oli.nagy@otherexample.net"
         ],
         "phone_numbers": [
            "5555555555",
            "1231231232"
         ]
      },
      ...
   ]

``GET /users/:id``
^^^^^^^^^^^^^^

Get all information about the user identified by ``id``.

::

   {
      "user_id": 1234,
      "employee_id": "DEV123",
      "username": "olinagy",
      "first_name": "Oliver",
      "last_name": "Nagy",
      "full_name": "Oliver Nagy",
      "role": "Deputy",
      "emails": [
         "oli.nagy@example.com",
         "oli.nagy@otherexample.net"
      ],
      "phone_numbers": [
         "5555555555",
         "1231231232"
      ]
   }

``PUT /users``
^^^^^^^^^^^^^^

Create a new user. Send JSON data in the request body.

::

      {
         "employee_id": "DEV123",
         "username": "olinagy",
         "first_name": "Oliver",
         "last_name": "Nagy",
         "full_name": "Oliver Nagy",
         "role": "Deputy",
         "phone": "5555555555",
         "mail": "info@example.com"
      }

``PATCH /users/:id``
^^^^^^^^^^^^^^

Update a user with the ``user_id``. Send JSON data in the request body.
You only need to send data that you want to update.

::

      {
         "first_name": "Oliver",
         "last_name": "Nagy"
      }

``GET /users/:user_id/timeoff/accrual/bank``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Return the current time off bank of the user with the ``user_id``.  
Example: ``GET /users/1234/timeoff/accrual/bank``::

   [
      {
         "hours": 1001,
         "time_off_type": {
            "id": 5,
            "name": "Sick"
         }
      },
      {
         "hours": -125.024,
         "time_off_type": {
            "id": 6,
            "name": "Vacation"
         }
      },
      {
         "hours": 29.2125,
         "time_off_type": {
            "id": 7,
            "name": "Earned Time"
         }
      },

      ...
   ]

``GET /users/:user_id/timeoff/accrual/profile``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Return the accrual type for each time off type based on the employee's accrual profile.

::

   [
      {
         "time_off_type": {
            "id": 5,
            "name": "Sick"
         },
         "accrual_type": "Accrues 10 hours every 28 days"
      },
      {
         "time_off_type": {
            "id": 6,
            "name": "Vacation"
         },
         "accrual_type": "Accrues one hour every 10 hours worked (max. 12 hours)"
      },
      {
         "time_off_type": {
            "id": 7,
            "name": "Earned Time"
         },
         "accrual_type": "No automatic accrual"
      },

      ...

   ]


.. _section-logs:

Logs
----

Whenever any change is made in the system, we add a system log entry. 
The endpoints below allow access to these system logs.

``GET /logs(/:after)``
^^^^^^^^^^^^^^

List the latest log entries. We return 50 log entries per page.
The ``prev`` and ``next`` links provide pagination through all of the system logs.

::

   {
      "data": [
         {
            "log_id": 329473,
            "created": "2016-10-13T16:35:52+0200",
            "event_description": "CallBack Staffing logged in.",
            "user": {
                "id": 1,
                "name": "CallBack Staffing",
                "href": "https://api.crewsense.com/v1/users/1"
            }
         },
         ...
      ],
      "pagination": {
         "prev": "https://api.crewsense.com/v1/logs/150",
         "next": "https://api.crewsense.com/v1/logs/250"
      }
   }


.. _section-announcements:

Announcements
-------------

Manage system announcements of your company.

``GET /announcements``
^^^^^^^^^^^^^^^^^^^^^^

Retrieve the latest, non-deleted announcements.
Example response::

   {
      "data": [
           {
               "id": 238,
               "company_id": 8,
               "title": "Test",
               "body": "<p>This is a great HTML <strong>message!</strong></p>",
               "first_name": "Boss",
               "last_name": "Doe",
               "user": {
                   "id": 848,
                   "name": "Boss Doe",
                   "href": "https://api.crewsense.com/v1/users/848"
               }
           },
           ...
       ],
       "pagination": {
           "prev": null,
           "next": null
       }
   }

``POST /announcements``
^^^^^^^^^^^^^^^^^^^^^^

Create a new company announcement.

+------------+---------------------------------------------------+
| Parameters |                                                   |
+============+===================================================+
| body       | The text of the announcement, in HTML. *Required* |
+------------+---------------------------------------------------+
| title      | Announcement title                                |
+------------+---------------------------------------------------+


``PUT /announcements/:id``
^^^^^^^^^^^^^^^^^^^^^^

Update a company announcement identified by ``:id``.

+------------+---------------------------------------------------+
| Parameters |                                                   |
+============+===================================================+
| body       | The text of the announcement, in HTML. *Required* |
+------------+---------------------------------------------------+
| title      | Announcement title                                |
+------------+---------------------------------------------------+


``DELETE /announcements/:id``
^^^^^^^^^^^^^^^^^^^^^^

Delete the announcement by the id ``id``.

.. _section-qualifiers:

Qualifiers
----------

``GET /qualifiers``
^^^^^^^^^^^^^^^^^^^

Retrieve all active qualifiers in your system.
Example response::

   [
      {
         "id": 7,
         "name": "Captain",
         "shortcode": "CPT",
         "modified_by": null,
         "modified_on": null
      },
      {
         "id": 8,
         "name": "Firefighter",
         "shortcode": "FF",
         "modified_by": null,
         "modified_on": null
      }
   ]

``GET /qualifiers/:id``
^^^^^^^^^^^^^^^^^^^

Retrieve all information about a qualifier.
Example response::

   {
      "id": 8,
      "name": "Firefighter",
      "shortcode": "FF-01",
      "title_id": 23,
      "created_by": 848,
      "created_on": "2015-11-18 05:19:27",
      "modified_by": null,
      "modified_on": null,
      "deleted_at": null,
      "deleted_by": null
   }


``POST /qualifiers``
^^^^^^^^^^^^^^^^^^^

Create a new qualifier.

+------------+------------------------------------------------------------------------+
| Parameters                                                                          |
+============+========================================================================+
| name       | Descriptive name of the qualifier                                      |
+------------+------------------------------------------------------------------------+
| shortcode  | Shortened name of the qualifier, to be displayed on the Crew Scheduler |
+------------+------------------------------------------------------------------------+

Example response::

   {
      "inserted": 1
   }


``DELETE /qualifiers/:id``
^^^^^^^^^^^^^^^^^^^

Delete a qualifier. The qualifier will be soft-deleted, which means we can manually
restore it if you think you've made a mistake deleting it.

``GET /qualifiers/:id/users``
^^^^^^^^^^^^^^^^^^^

Retrieve all associated users of a qualifier.
Example response::

   [
      {
         "id": 98,
         "name": "Hass Brycen",
         "ranking": 0
      },
      {
         "id": 138,
         "name": "Oliver Nagy",
         "ranking": 0
      },
      ...
   ]