Resources
=========

``/schedule``
----------------

The schedule consists of *shifts*, *time offs*, *callbacks*, *trades*, *misc. hours* and *group events*.
They are wrapped by a top-level object containing metadata about the requested schedule (start date, end date, links for the next and previous period).

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
      },
   }

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
      "date": "2015-03-15",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "name": "Station 1",
      "positions_to_fill": 3,
      "recurring": true,
      "shifts": [
         ... see next section ...
      ]
   }

Let's refer to one of these object as an ``asssignment``.

``day.assignment.shifts``
^^^^^^^^^^^^^^^^^^^^^^^^^^

This array holds data about the employees scheduled for the assignment on the given day. An object of this array is formatted 
like this::

   {
      "id": 456789,
      "href": "https://callbackstaffing.com/api/v1/shift/456789",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "hold_over": 0,
      "recurring": true,
      "recurrence_start": "2014-12-11",
      "user": {
         "id": 848,
         "href": "https://callbackstaffing.com/api/v1/user/848",
         "name": "John Doe"
      },
      "admin": {
         "id": 138,
         "href": "https://callbackstaffing.com/api/v1/user/138",
         "name": "Joe Boss"
      },
      "work_type": {
         "id": 33,
         "href": "https://callbackstaffing.com/api/v1/work_type/33",
         "name": "Regular Time",
         "work_code": "REG001"
      },
      "labels": [
         {
            "id": 12,
            "href": "https://callbackstaffing.com/api/v1/label/12",
            "label": "ENG"
         }
      ]
   }

You will notice that some of the included objects have ``href`` properties. This is because we are only returning a sensible 
subset of the available data about these objects. If you make a ``GET`` request to the provided URL, you can retrieve all of 
the available information about them. @TODO See ``/user``, ``/work_type`` and ``/label`` for details.

``day.time_off``
^^^^^^^^^^^^^^^^

All approved time off for the day is in this array, including long term and recurring leave that has an occurrence fall on this 
day. The general structure of one object in the array::

   {
      "id": 623492,
      "href": "https://callbackstaffing.com/api/v1/time_off/623492",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "recurring": false,
      "user": {
         "id": 848,
         "href": "https://callbackstaffing.com/api/v1/user/848",
         "name": "John Doe"
      },
      "admin": {
         "id": 138,
         "href": "https://callbackstaffing.com/api/v1/user/138",
         "name": "Joe Boss"
      },
      "time_off_type": {
         "id": 45,
         "href": "https://callbackstaffing.com/api/v1/time_off_type/45",
         "name": "Sick Leave",
         "work_code": "SL"
      }
   }

``day.callbacks``
^^^^^^^^^^^^^^^^

In this array you will find all finalized callbacks for the day. Callback shifts that were drag & dropped to a work assignment 
will not be included, they are under ``day.assignment.shifts``. A ``callback`` object is structured like this::

   {
      "id": 64012,
      "href": "https://callbackstaffing.com/api/v1/callback/64012",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "positions_to_fill": 1,
      "records": [
         {
            "id": 2165743,
            "user": {
               "id": 848,
               "href": "https://callbackstaffing.com/api/v1/user/848",
               "name": "John Doe"
            },
            "start": "2015-03-15 08:00:00",
            "end": "2015-03-16 08:00:00",
            "work_site": null
         }
      ]
      "title": {
         "id": 112,
         "href": "https://callbackstaffing.com/api/v1/title/112",
         "name": "Firefighter"
      }
   }

``records`` gives you all accepting employees of the callback. You can request more data about certain pieces of the callback 
using the ``href`` links provided.

``day.trades``
^^^^^^^^^^^^^^^^

``trades`` contains all accepted and finalized shift trades for the day. A trade object in the array looks like this::

   {
      "id": 4355,
      "href": "https://callbackstaffing.com/api/v1/trade/4355",
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-16 08:00:00",
      "requesting_user": {
         "id": 848,
         "href": "https://callbackstaffing.com/api/v1/user/848",
         "name": "John Doe"
      },
      "accepting_user": {
         "id": 138,
         "href": "https://callbackstaffing.com/api/v1/user/138",
         "name": "Jack Smith"
      }
   }

Follow the top-level ``href`` link to receive all information about the trade.