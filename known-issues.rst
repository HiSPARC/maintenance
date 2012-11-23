.. include:: subst.inc


Known issues
============

This is a list of known possible issues with |hisparc| stations.
Each problem notes whether it has been fixed in a specific
version of the software and if it was perhaps introduced by one.
Also other parameters that could affect the occurance of the issue
are be noted (e.g. Windows XP/7, RAM, cable lengths).
Moreover, the Nagios warnings that an issue can trigger are mentioned.

.. note:: Multiple issues can cause the same Nagios warning.

Structure
---------

Each problem has the following fields:

:Occuring Since: Software version that introduced it.
:Fixed Since: Software version that fixed this problem (problems may persist if you upgrade from older versions).
:First Sign: Explaining how you will probably notice the problem.
:Nagios: Nagios warnings that can be triggered.
:Determination: This is a small guide explaining how to make sure that the problem being described is what you are experiencing.
:Solution: How to solve it.
:Effects: The effects of this problem.
:Keywords: Some words that relate to this problem.

.. Template
   --------
   
   Subject
   ^^^^^^^

   :Occuring Since:
   :Fixed Since:
   :First Sign:
   :Nagios:
   :Determination:
   :Solution:
   :Effects:
   :Keywords:

Software
========

This sections concerns itself with issues related to the |hisparc|
station-software.


HiSPARC Monitor does not start
------------------------------

Missing directory
^^^^^^^^^^^^^^^^^
:Occuring Since: v6.9.0
:Fixed Since: v6.10.0
:First Sign: When the :code:`STARTHiSPARCSoftware` program runs and the other programs (|hisparc| DAQ and Updater) start normally but the |hisparc| Monitor does not appear or closes instantly.
:Nagios: **EventRate**, **StorageGrowth**, **StorageSize**, **TriggerRate**, and possibly **Buffer size**
:Determination:
    * Look in :code:`hisparc/persistent/logs/src/` for the latest log file.
    * Check if there is a line that contains the text :code:`Error: unable to open database file`.
    * Look in the :code:`hisparc/persistent/data/` directory for a :code:`hsmonitor` folder.
    * If it does not exist than go to the solution, otherwise this is not the problem.
:Solution: Create the missing :code:`hsmonitor` directory in :code:`hisparc/persistent/data`. 
:Effects: The missing directory causes the |hisparc| Monitor to be unable to store events in its SQLite database, preventing it from sending events to the Nikhef datastore. Note that the |hisparc| DAQ should be unaffected. No events should be lost, the DAQ will store events in its MySQL database until the hard disc fills up.
:Keywords:


Hard Disc Space
---------------

To many logs
^^^^^^^^^^^^

:Occuring Since:
:Fixed Since: not yet
:First Sign: Nagios warning about Disc Space.
:Nagios: **Disc Space**.
:Determination:
    * Look in hisparc/persistent/logs/.
    * Check the size of the src directory by right-clicking on it and choosing 'Properties'.
    * Check if this is a significant fraction of the total disc space.
:Solution: Remove all logs from the src directory except for the current one (present date in :code:`dd-mm-yyyy.log`). Select all (ctrl + a) logs in :code:`hisparc/persistent/logs/src`. Deselect the current one (ctrl + click). Remove them using shift + delete (to bypass the Recycle Bin)
:Effects: If the disc is full the |hisparc| daq can not store events in the database, preventing the station from storing more events.
:Keywords:

To many updaters
^^^^^^^^^^^^^^^^

:Occuring Since:
:Fixed Since: not yet
:First Sign: Nagios warning about Disc Space.
:Nagios: **Disc Space**
:Determination:
    * Look in hisparc/persistent/downloads/.
    * There should be some adminUpdater\_v##.zip and userUnpacker\_v##.exe files there.
    * By right-clicking them you can see their file size is of the order of 100 MB.
    * If there are many they can take up some space.
:Solution: Remove all userUnpacker and adminUpdater files except the newest ones. Do this by selecting them and pressing shift + delete to remove them directly.
:Effects: If the disc is full the |hisparc| daq can not store events in the database, preventing the station from storing more events.
:Keywords:


|hisparc| DAQ Errors
--------------------

Can not connect to buffer
^^^^^^^^^^^^^^^^^^^^^^^^^

:Occuring Since:
:Fixed Since:
:First Sign: Red LED in |hisparc| DAQ
:Nagios:
:Determination: From the Start menu start odbcad32.exe. Check if the hisparc buffer is there.
:Solution:
:Effects:
:Keywords:

Not in DAQ Mode
^^^^^^^^^^^^^^^

:Occuring Since:
:Fixed Since:
:First Sign:
:Nagios: **TriggerRate**
:Determination:
:Solution:
:Effects:
:Keywords:


Error in |hisparc| Monitor
--------------------------

Malformed HisparcII.ini
^^^^^^^^^^^^^^^^^^^^^^^

:Occuring Since:
:Fixed Since:
:First Sign: Errors in the |hisparc| Monitor: :code:`Uncatched exception in job: need more than 1 value to unpack. Restarting...`
:Nagios: **TriggerRate**
:Determination: Check in the file hisparc/persistent/configuration/HisparcII.ini for blank lines
:Solution: Remove any blank lines from HisparcII.ini
:Effects: Errors in the |hisparc| Monitor and no TriggerRate updates for Nagios.
:Keywords:


Hardware
========
