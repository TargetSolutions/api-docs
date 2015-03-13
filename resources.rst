Resources
=========

``/v1/schedule``
----------------

The schedule consists of *shifts*, *time offs*, *callbacks*, *trades*, *misc. hours* and *group events*.
They are wrapped by a top-level object containing metadata about the requested schedule (start date, end date, links for the next and previous period).

An example of returned schedule data looks like this::

   {
      "start": "2015-03-15 08:00:00",
      "end": "2015-03-22 08:00:00",

      "shifts": [
         ... shift data ...
      ],

      "prev": {
         "href": "https://callbackstaffing.com/api/v1/schedule?start=2015-03-08%2008:00:00&end=2015-03-15%2008:00:00"
      },
      "next": {
         "href": "https://callbackstaffing.com/api/v1/schedule?start=2015-03-22%2008:00:00&end=2015-03-22%2008:00:00"
      },
   }

