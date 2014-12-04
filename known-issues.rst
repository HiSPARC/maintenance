:tocdepth: 3

.. include:: subst.inc

############
Known issues
############

This is a list of known possible issues with |hisparc| stations. For
each problem some steps are given which can be followed to determine if
that problem is indeed occuring on your station. Possible Nagios Service
warnings that might alert you to the problem are noted.

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
:Determination: This is a small guide explaining how to make sure that
                the problem being described is what you are experiencing.
:Solution: How to solve it.
:Effects: The effects of this problem.

.. :Fixed: True if it is fixed in the latest version.
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

   .. :Fixed:
      :Keywords:


Software
========

This sections concerns itself with issues related to the |hisparc|
station-software.


HiSPARC Monitor does not start
------------------------------

Missing directory
^^^^^^^^^^^^^^^^^

:First Sign: When the :code:`STARTHiSPARCSoftware` program runs and the
             other programs (|hisparc| DAQ and Updater) start normally
             but the |hisparc| Monitor does not appear or closes
             instantly.
:Nagios: **EventRate**, **StorageGrowth**, **StorageSize**,
         **TriggerRate**, and possibly **Buffer size**
:Determination:
    * Look in :code:`hisparc/persistent/logs/src/` for the latest log
      file.
    * Check if there is a line that contains the text :code:`Error:
      unable to open database file`.
    * Look in the :code:`hisparc/persistent/data/` directory for a
      :code:`hsmonitor` folder.
    * If it does not exist than go to the solution, otherwise this is
      not the problem.
:Solution: Create the missing :code:`hsmonitor` directory in
           :code:`hisparc/persistent/data`.
:Effects: The missing directory causes the |hisparc| Monitor to be
          unable to store events in its SQLite database, preventing it
          from sending events to the Nikhef datastore. Note that the
          |hisparc| DAQ should be unaffected. No events should be lost,
          the DAQ will store events in its MySQL database until the hard
          disc fills up.

.. :Fixed: True
   :Keywords:


Hard Disc Space
---------------

To many logs
^^^^^^^^^^^^

:First Sign: Nagios warning about Disc Space.
:Nagios: **Drive Space**
:Determination:
    * Look in :code:`hisparc/persistent/logs/`.
    * Check the size of the src directory by right-clicking on it and
      choosing 'Properties'.
    * Check if this is a significant fraction of the total disc space.
:Solution: Remove all logs from the src directory except for the current
           one (present date in :code:`dd-mm-yyyy.log`). Select all
           (ctrl + a) logs in :code:`hisparc/persistent/logs/src`.
           Deselect the current one (ctrl + click). Remove them using
           shift + delete (to bypass the Recycle Bin)
:Effects: If the disc is full the |hisparc| daq can not store events in
          the database, preventing the station from storing more events.

.. :Fixed:
   :Keywords:


To many updaters
^^^^^^^^^^^^^^^^

:First Sign: Nagios warning about Disc Space.
:Nagios: **Drive Space**
:Determination:
    * Look in :code:`hisparc/persistent/downloads/`.
    * There should be some adminUpdater\_v##.zip and
      userUnpacker\_v##.exe files there.
    * By right-clicking them you can see their file size is of the order
      of 100 MB.
    * If there are many they can take up some space.
:Solution: Remove all userUnpacker and adminUpdater files except the
           newest ones. Do this by selecting them and pressing shift +
           delete to remove them directly.
:Effects: If the disc is full the |hisparc| daq can not store events in
          the database, preventing the station from storing more events.

.. :Fixed:
   :Keywords:


|hisparc| DAQ Errors
--------------------

Can not connect to buffer
^^^^^^^^^^^^^^^^^^^^^^^^^

:First Sign: Red LED in |hisparc| DAQ
:Nagios:
:Determination: From the Start menu start odbcad32.exe. Check if the
                hisparc buffer is there.
:Solution:
:Effects: The |hisparc| DAQ will not be able to store events.

.. :Fixed:
   :Keywords:


Not in DAQ Mode
^^^^^^^^^^^^^^^

:First Sign:
:Nagios: **TriggerRate**
:Determination: Look at the program |hispdaq|, see if the button in the
                middle shows 'DAQ Mode'.
:Solution: Click the 'DAQ Mode' button in the |hispdaq|.
:Effects: When the |hispdaq| is not in DAQ Mode it will not store
          triggered events.

.. :Fixed:
   :Keywords:


Error in |hisparc| Monitor
--------------------------

Malformed HisparcII.ini
^^^^^^^^^^^^^^^^^^^^^^^

:First Sign: Errors in the |hisparc| Monitor: :code:`Uncatched exception
             in job: need more than 1 value to unpack. Restarting...`
:Nagios: **TriggerRate**
:Determination: Check for blank lines in the file
                :code:`hisparc/persistent/configuration/HisparcII.ini`.
:Solution: Remove any blank lines from HisparcII.ini
:Effects: Errors in the |hisparc| Monitor and no TriggerRate updates for
          Nagios.

.. :Fixed:
   :Keywords:


Time difference to large
^^^^^^^^^^^^^^^^^^^^^^^^

:First Sign: Errors in the |monitor|: :code:`Uncatched exception in job:
             invalid literal for int() with base 10: 'difference too
             large'. Restarting...`
:Nagios: **TriggerRate**
:Determination: Check for the text :code:`difference to large` in
                :code:`hisparc/persistent/configuration/HisparcII.ini`.
:Solution: Check the PC time, make sure that it is set to the current
           time. Check the GPS settings, make sure that it is working
           and showing the correct GPS time.
:Effects: Errors in the |hisparc| Monitor and no TriggerRate updates for
          Nagios.

.. :Fixed:
   :Keywords:


400 Bad Request
^^^^^^^^^^^^^^^

:First Sign: Errors in the |monitor|: :code:`Error Uploader: .. Return
             code: 400`
:Nagios: **StorageSize**, **TriggerRate**
:Determination: Ensure that all required variables are being uploaded:
                station_id, password, data and the checksum.
:Solution: Check that the station number and password are entered
           correctly in the configuration file:
           :code:`hisparc/persistent/configuration/config.ini`.
:Effects: No data will be uploaded.

.. :Fixed:
   :Keywords:


Access denied for MySQL buffer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

:First Sign: Error in Nagios: :code:`Buffer size: Access denied for user
             'buffer'@'localhost'`
:Nagios: **Buffer size**
:Determination: Check if the file :code:`hisparc/user/diagnosticchecks/checks.py`
                contains only one path to a CONFIG_INI:
                :code:`CONFIG_INI = "../../user/hsmonitor/data/config.ini"`.
:Solution: Open the file :code:`hisparc/user/diagnosticchecks/checks.py`
           in a text editor like NotePad and add the following line after
           the line starting with :code:`CONFIG_INI = "../..`:
           :code:`CONFIG_INI2 = "../../user/hsmonitor/data/config-password.ini"`.
           Finally several lines later is the following code:
           :code:`config.read(CONFIG_INI)`, replace this by:
           :code:`config.read([CONFIG_INI, CONFIG_INI2])`.
           Save the file.
:Effects: Nagios can not monitor the Buffer size.

.. :Fixed:
   :Keywords:


GPS
---

Firmware not loaded
^^^^^^^^^^^^^^^^^^^

:First Sign: No GPS appears in DSP Mon
:Nagios: 
:Determination: This only occurs with |hisparc| III electronics when
                their firmware is not yet loaded, which is indicated by
                all LEDs on the unit being on.
:Solution: Start the |hisparc| DAQ to load the firmware into the
           |hisparc| electronics.
:Effects: No GPS recognized by DSP Mon.

.. :Fixed:
   :Keywords:


COM Port to high
^^^^^^^^^^^^^^^^

:First Sign: The GPS date attatched to events is very inaccurate, like
             ~1999 or ~2019.
:Nagios: 
:Determination:
    * Open Configuration -> System -> Hardware -> Browse Devices -> Com
      Ports.
    * If the Com ports number higher than 32 the DSP Mon GPS program can
      not connect to the GPS.
:Solution: Lower the COM Port Number, by direct reassignment, use the
           com\_port\_reassign utility.
:Effects: No GPS recognized by DSP Mon, this causes data to get dates
          like 1999 or 2019, making the data unusable.

.. :Fixed:
   :Keywords:


No antenna connected
^^^^^^^^^^^^^^^^^^^^

:First Sign: The GPS get no satelite signals, seen in the Satelites tab
             of the |hisparc| DAQ.
:Nagios: 
:Determination: Open DSP Mon and check the LED status of the
                :code:`Antenna Open`. It will be yellow while some of
                the other LEDs are green. Also all Signal Values (SV)
                will be 0.
:Solution: Check the GPS cable for kinks or cuts, also check if the
           antenna is still properly attached. Once fixed the SV should
           rise and turn green, as well as the :code:`Antenna Open`
           status.
:Effects: Makes the GPS unable to determine the correct date. Is falls
          back to some other date like 1999 or 2019, making the data
          unusable.

.. :Fixed:
   :Keywords:


Time offset
^^^^^^^^^^^

:First Sign: No coincidences with nearby stations.
:Nagios: 
:Determination: Open DSP Mon and check if the timing for the GPS is set
                to UTC or GPS, it should be GPS.
:Solution: Set the GPS Timing to use GPS time.
:Effects: There is a difference of ~16 seconds between GPS en UTC time,
          resulting in offset timestamps if the wrong setting is chosen.

.. :Fixed:
   :Keywords:


Uploading
---------

Proxy not set
^^^^^^^^^^^^^

:First Sign: No data is uploaded, the local storage fills with events.
:Nagios: **StorageSize**
:Determination: Run Diagnostics (LocalDiagnosticTool in older versions)
                to check if a proxy is required.
:Solution: Run Diagnostics (LocalDiagnosticTool in older versions) to
           check Proxy settings, if it finds proxy settings for the
           system it can use these to configure them for Python. Press
           the Write Config.
:Effects:

.. :Fixed:
   :Keywords:


Firewall
--------

VPN blocked
^^^^^^^^^^^

:First Sign: All Status indicators on Nagios are CRITICAL.
:Nagios: **Host**, **Buffer size**, **CPU Load**, **Drive Space**,
         **EventRate**, **Labview Usage**, **Memory Usage**,
         **StorageGrowth**, **StorageSize**, **TriggerRate**, **Uptime**
:Determination: Run Diagnostics (LocalDiagnosticTool in older versions)
                to check the VPN status.
:Solution: Open TCP port 443 in the firewalls.
:Effects: Nagios will be unable to check the status of all services.
          Moreover, the |hisparc| support will be unable to log into the
          PC remotely to assist in case of problems.

.. :Fixed:
   :Keywords:


Web blocked
^^^^^^^^^^^

:First Sign: No data is uploaded, the local storage fills with events.
:Nagios: **StorageSize**
:Determination: Try opening a website in a browser on the detector PC,
                preferably www.nikhef.nl, if this fails then web traffic
                is blocked. If the browser has no problems, then look at
                the `Proxy not set`_ issue.
:Solution: Open port 80 in the firewalls
:Effects: The Uploader of the |hisparc| Monitor uses a HTTP POST Request
          to send data to our datastore, but this will be blocked if
          port 80 is closed.

.. :Fixed:
   :Keywords:


.exe blocked
^^^^^^^^^^^^

:First Sign: An update is available but the software can not download it.
:Nagios: ..
:Determination: Try opening the link to the updater, shown in the
                |hisparc| Updater, in a webbrowser. If this fails then
                .exe files are probably blocked by the network.
:Solution: Allow downloading of .exe files through the firewall.
:Effects: The Updater will see there is an update and quit the |hisparc|
          DAQ. However when the update fail it will restart the
          |hisparc| software, but then see there is an update and try
          again, it will be stuck in this loop.

.. :Fixed:
   :Keywords:


Hardware
========

This sections concerns itself with issues related to the |hisparc|
electronics and hardware.


Bad signals
-----------

Bad power supply
^^^^^^^^^^^^^^^^

:First Sign: |hisparc| DAQ might frequently loose the connection to the
             |hisparc| electronics or there will be fluctuations (sine)
             over the signal.
:Nagios: 
:Determination: Replace the power supply with a new one and see if the
                problem disappears.
:Solution: Replace the power supply.
:Effects: 

.. :Fixed:
   :Keywords:


Light leak
^^^^^^^^^^

:First Sign: Many small peaks (short pulses) in the signals in the
             |hisparc| DAQ, also the number of events will increase
             during day time (due to sunlight)
:Nagios: **TriggerRate**
:Determination: Cover the detector with a light-tight blanket or foil.
                Now the extra peaks should disappear.
:Solution: Patch the light-leaking parts with new foil/tape.
:Effects: 

.. :Fixed:
   :Keywords:


Bad PMT base
^^^^^^^^^^^^

:First Sign: The current for one of the PMTs is very high (above 15 mA).
:Nagios:
:Determination: Look in the |hisparc| DAQ at the current used by the
                PMTs. If this is above 15 mA something if probably wrong.
:Solution: Try lowering the High Voltage on the PMT, or turning it off
           for a day. If that does not help it may need to be replaced,
           contact your cluster coordinator.
:Effects: The PMT will not function properly.

.. :Fixed:
   :Keywords:


.. Signal reflections
   ^^^^^^^^^^^^^^^^^^

   :First Sign: Small pulse going above the baseline (positive mV),
                often the same shape as a preceding signal.
   :Nagios: 
   :Determination: Look for small upward pulses in the |hisparc| DAQ, it
                   might help to turn off data reduction to see more of
                   the pulse after the actual.
   :Solution: 
   :Effects: 

   .. :Fixed:
      :Keywords:


No Devices Found
----------------

Connect to power
^^^^^^^^^^^^^^^^

:First Sign: |hisparc| DAQ is unable to connect to the |hisparc|
             electronics.
:Nagios: **EventRate**, **StorageGrowth**, **StorageSize**,
         **TriggerRate**
:Determination: Start the |hisparc| DAQ, it will show a message that no
                device is found. Check if the LEDs on the |hisparc|
                electronics box are lit.
:Solution: Connect the |hisparc| electronic box via the provided Power
           supply to a power outlet.
:Effects: No data can be taken.

.. :Fixed:
   :Keywords:
