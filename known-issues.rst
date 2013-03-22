:tocdepth: 3

.. include:: subst.inc

############
Known issues
############

This is a list of known possible issues with |hisparc| stations. For each problem some steps are given which can be followed to determine if that problem is indeed occuring on your station. Possible Nagios Service warnings that might alert you to the problem are noted.

Here are filters to only show problems which cause a certain Nagios warning:
`Buffer size <?nagios=Buffer size>`_,
`CPU Load <?nagios=CPU Load>`_,
`Drive Space <?nagios=Drive Space>`_,
`EventRate <?nagios=EventRate>`_,
`Labview Usage <?nagios=Labview Usage>`_,
`Memory Usage <?nagios=Memory Usage>`_,
`StorageGrowth <?nagios=StorageGrowth>`_,
`StorageSize <?nagios=StorageSize>`_,
`TriggerRate <?nagios=TriggerRate>`_,
`Uptime <?nagios=Uptime>`_.

.. note:: Multiple issues can cause the same Nagios warning.

Each problem described below has the following fields:

:First Sign: Explaining how you will probably notice the problem.
:Nagios: Nagios warnings that can be triggered.
:Determination: This is a small guide explaining how to make sure that the problem being described is what you are experiencing.
:Solution: How to solve it.
:Effects: The effects of this problem.

.. :Occuring Since: Software version that introduced it.
   :Fixed Since: Software version that fixed this problem (problems may persist if you upgrade from older versions).
   :Keywords: Some words that relate to this problem.

.. Template
   --------
   
   Subject
   ^^^^^^^

   :First Sign:
   :Nagios:
   :Determination:
   :Solution:
   :Effects:

   .. :Occuring Since:
      :Fixed Since:
      :Keywords:

Software
========

This sections concerns itself with issues related to the |hisparc|
station-software.


HiSPARC Monitor does not start
------------------------------

Missing directory
^^^^^^^^^^^^^^^^^

:First Sign: When the :code:`STARTHiSPARCSoftware` program runs and the other programs (|hisparc| DAQ and Updater) start normally but the |hisparc| Monitor does not appear or closes instantly.
:Nagios: **EventRate**, **StorageGrowth**, **StorageSize**, **TriggerRate**, and possibly **Buffer size**
:Determination:
    * Look in :code:`hisparc/persistent/logs/src/` for the latest log file.
    * Check if there is a line that contains the text :code:`Error: unable to open database file`.
    * Look in the :code:`hisparc/persistent/data/` directory for a :code:`hsmonitor` folder.
    * If it does not exist than go to the solution, otherwise this is not the problem.
:Solution: Create the missing :code:`hsmonitor` directory in :code:`hisparc/persistent/data`. 
:Effects: The missing directory causes the |hisparc| Monitor to be unable to store events in its SQLite database, preventing it from sending events to the Nikhef datastore. Note that the |hisparc| DAQ should be unaffected. No events should be lost, the DAQ will store events in its MySQL database until the hard disc fills up.

.. :Occuring Since: v6.9.0
   :Fixed Since: v6.10.0
   :Keywords:


Hard Disc Space
---------------

To many logs
^^^^^^^^^^^^

:First Sign: Nagios warning about Disc Space.
:Nagios: **Drive Space**
:Determination:
    * Look in hisparc/persistent/logs/.
    * Check the size of the src directory by right-clicking on it and choosing 'Properties'.
    * Check if this is a significant fraction of the total disc space.
:Solution: Remove all logs from the src directory except for the current one (present date in :code:`dd-mm-yyyy.log`). Select all (ctrl + a) logs in :code:`hisparc/persistent/logs/src`. Deselect the current one (ctrl + click). Remove them using shift + delete (to bypass the Recycle Bin)
:Effects: If the disc is full the |hisparc| daq can not store events in the database, preventing the station from storing more events.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:

To many updaters
^^^^^^^^^^^^^^^^

:First Sign: Nagios warning about Disc Space.
:Nagios: **Drive Space**
:Determination:
    * Look in hisparc/persistent/downloads/.
    * There should be some adminUpdater\_v##.zip and userUnpacker\_v##.exe files there.
    * By right-clicking them you can see their file size is of the order of 100 MB.
    * If there are many they can take up some space.
:Solution: Remove all userUnpacker and adminUpdater files except the newest ones. Do this by selecting them and pressing shift + delete to remove them directly.
:Effects: If the disc is full the |hisparc| daq can not store events in the database, preventing the station from storing more events.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


|hisparc| DAQ Errors
--------------------

Can not connect to buffer
^^^^^^^^^^^^^^^^^^^^^^^^^

:First Sign: Red LED in |hisparc| DAQ
:Nagios:
:Determination: From the Start menu start odbcad32.exe. Check if the hisparc buffer is there.
:Solution:
:Effects: The |hisparc| DAQ will not be able to store events.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:

Not in DAQ Mode
^^^^^^^^^^^^^^^

:First Sign:
:Nagios: **TriggerRate**
:Determination: Look at the program |hispdaq|, see if the button in the middle shows 'DAQ Mode'.
:Solution: Click the 'DAQ Mode' button in the |hispdaq|.
:Effects: When the |hispdaq| is not in DAQ Mode it will not store triggered events.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


Error in |hisparc| Monitor
--------------------------

Malformed HisparcII.ini
^^^^^^^^^^^^^^^^^^^^^^^

:First Sign: Errors in the |hisparc| Monitor: :code:`Uncatched exception in job: need more than 1 value to unpack. Restarting...`
:Nagios: **TriggerRate**
:Determination: Check in the file hisparc/persistent/configuration/HisparcII.ini for blank lines
:Solution: Remove any blank lines from HisparcII.ini
:Effects: Errors in the |hisparc| Monitor and no TriggerRate updates for Nagios.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


Time difference to large
^^^^^^^^^^^^^^^^^^^^^^^^

:First Sign: Errors in the |monitor|: :code:`Uncatched exception in job: invalid literal for int() with base 10: 'difference too large'. Restarting...`
:Nagios: **TriggerRate**
:Determination: Check in the file hisparc/persistent/configuration/HisparcII.ini for the text 'difference to large'.
:Solution: Check the PC time, make sure that it is set to the current time. Check the GPS settings, make sure that it is working and showing the correct GPS time.
:Effects: Errors in the |hisparc| Monitor and no TriggerRate updates for Nagios.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


GPS
---

COM Port to high
^^^^^^^^^^^^^^^^

:First Sign: The GPS date attatched to events is very inaccurate, like ~1999 or ~2019.
:Nagios: 
:Determination:
    * Open Configuration -> System -> Hardware -> Browse Devices -> Com Ports
    * If the Com ports number higher than 32 the DSP Mon GPS program can not connect to the GPS
:Solution: Lower the COM Port Number, by direct reassignment, use the com\_port\_reassign utility.
:Effects: No GPS recognized by DSP Mon, this causes data to get dates like 1999 or 2019, making the data unusable.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


No antenna connected
^^^^^^^^^^^^^^^^^^^^

:First Sign: The GPS get no satelite signals, seen in the Satelites tab of the |hisparc| DAQ.
:Nagios: 
:Determination: Open DSP Mon and check the LED status of the :code:`Antenna Open`. It will be yellow while some of the other LEDs are green. Also all Signal Values (SV) will be 0.
:Solution: Check the GPS cable for kinks or cuts, also check if the antenna is still properly attached. Once fixed the SV should rise and turn green, as well as the :code:`Antenna Open` status.
:Effects: Makes the GPS unable to determine the correct date. Is falls back to some other date like 1999 or 2019, making the data unusable.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


Uploading
---------

Proxy not set
^^^^^^^^^^^^^

:First Sign: No data is uploaded, the local storage fills with events
:Nagios: **StorageSize**
:Determination: Run Diagnostics (LocalDiagnosticTool in older versions) to check if a proxy is required.
:Solution: Run Diagnostics (LocalDiagnosticTool in older versions) to check Proxy settings, if it finds proxy settings for the system it can use these to configure them for Python. Press the Write Config.
:Effects:

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


Firewall
--------

VPN blocked
^^^^^^^^^^^

:First Sign: All Status indicators on Nagios are CRITICAL
:Nagios: **Host**, **Buffer size**, **CPU Load**, **Drive Space**, **EventRate**, **Labview Usage**, **Memory Usage**, **StorageGrowth**, **StorageSize**, **TriggerRate**, **Uptime**
:Determination: Run Diagnostics (LocalDiagnosticTool in older versions) to check the VPN status.
:Solution: Open TCP port 443 in the firewalls
:Effects: Nagios will be unable to check the status of all services. Moreover, the |hisparc| support will be unable to log into the PC remotely to assist in case of problems.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


Web blocked
^^^^^^^^^^^

:First Sign: No data is uploaded, the local storage fills with events
:Nagios: **StorageSize**
:Determination: Try opening a website in a browser on the detector PC, preferably www.nikhef.nl, if this fails then web traffic is blocked. If the browser has no problems, then look at the `Proxy not set`_ issue.
:Solution: Open port 80 in the firewalls
:Effects: The Uploader of the |hisparc| Monitor uses a HTTP POST Request to send data to our datastore, but this will be blocked if port 80 is closed.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


.exe blocked
^^^^^^^^^^^^

:First Sign: An update is available but the software can not download it
:Nagios: ..
:Determination: Try opening the link to the updater, shown in the |hisparc| Updater, in a webbrowser. If this fails then .exe files are probably blocked by the network.
:Solution: Allow downloading of .exe files through the firewall
:Effects: The Updater will see there is an update and quit the |hisparc| DAQ. However when the update fail it will restart the |hisparc| software, but then see there is an update and try again, it will be stuck in this loop.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:


Hardware
========

This sections concerns itself with issues related to the |hisparc|
electronics and hardware.

No Devices Found
----------------

Connect to power
^^^^^^^^^^^^^^^^

:First Sign: |hisparc| DAQ s unable to connect to the |hisparc| electronics
:Nagios: **EventRate**, **StorageGrowth**, **StorageSize**, **TriggerRate**
:Determination: Start the |hisparc| DAQ, it will show a message that no device is found. Check if the LEDs on the |hisparc| electronics box are lit.
:Solution: Connect the |hisparc| electronic box via the provided Power supply to a power outlet.
:Effects: No data can be taken.

.. :Occuring Since:
   :Fixed Since:
   :Keywords:
