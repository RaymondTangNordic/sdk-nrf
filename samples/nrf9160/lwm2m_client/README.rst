.. _lwm2m_client:

nRF9160: LwM2M Client
#####################

.. contents::
   :local:
   :depth: 2

The LwM2M Client demonstrates how to use Lightweight Machine to Machine (`LwM2M`_) protocol to connect an nRF9160 DK to an LwM2M server such as `Leshan Demo Server`_ via LTE.
Once connected, the device can be queried to obtain GPS and sensor data, and to retrieve information about the modem.
This sample uses the :ref:`lib_lwm2m_client_utils` library.

Requirements
************
The sample supports the following development kit:

.. table-from-rows:: /includes/sample_board_rows.txt
   :header: heading
   :rows: nrf9160dk_nrf9160_ns

The sample also requires an LwM2M server URL address available on the internet.
For this sample, the URL address mentioned on the `Leshan Demo Server`_ page is used.

.. include:: /includes/spm.txt

Overview
********

`LwM2M`_ is an application layer protocol based on CoAP/UDP.
It is designed to expose various resources for reading, writing and executing via an LwM2M server in a very lightweight environment.
The nRF9160 sample sends data such as button and switch states, accelerometer data (the physical orientation of the device), temperature and GPS position.
It can also receive activation commands such as buzzer activation and light control.

.. list-table::
   :align: center

   * - Button states
     - DOWN/UP
   * - Switch states
     - ON/OFF
   * - Accelerometer data
     - FLIP
   * - Temperature
     - TEMP
   * - GPS coordinates
     - GPS
   * - Buzzer
     - TRIGGER
   * - Light control
     - ON/OFF

.. _dtls_support:

DTLS Support
============

The sample has DTLS security enabled by default.
You need to provide the following information to the LwM2M server before you can make a successful connection:

* Client endpoint
* Identity
* Pre-shared key

The following instructions describe how to register your device and these instructions are specific to `Leshan Demo Server`_:

1. Open the Leshan Demo Server web UI.
#. Click on :guilabel:`SECURITY` in the upper-right corner.
#. Click on :guilabel:`ADD SECURITY INFORMATION`.
#. Enter the following data and click :guilabel:`ADD`:

    * Endpoint - nrf-*your Device IMEI*
    * Security Mode - psk
    * Identity: - nrf-*your Device IMEI*
    * Key - 000102030405060708090a0b0c0d0e0f

#. :ref:`Build and run the LwM2M Client sample <build_lwm2m>`.

Configuration
*************

|config|

Configuration options
=====================

Check and configure the following configuration options for the sample:

.. option:: CONFIG_APP_LWM2M_SERVER - LWM2M Server configuration

   The sample configuration specifies the LWM2M Server to be used.
   In this sample, you can set this option to ``leshan.eclipseprojects.io`` (`public Leshan Demo Server`_).
   You must not prefix it with the application protocol.


Additional configurations
=========================

Check and configure the following library options that are used by the sample:

* :kconfig:`CONFIG_LWM2M_CLIENT_UTILS` - Enables the utility library :ref:`lib_lwm2m_client_utils`, which initializes a predefined set of objects.

* :kconfig:`CONFIG_LWM2M_IPSO_SUPPORT` - Enables support for different IPSO objects. If you are disabling this option, you must disable all the individual configuration options that enables the sensor.


Configuration files
===================

The sample provides predefined configuration files for typical use cases.
You can find the configuration files in the :file:`samples/nrf9160/lwm2m_client` directory.

The following files are available:

* :file:`prj.conf` - Standard default configuration file
* :file:`overlay-queue` - Enables LwM2M Queue Mode support
* :file:`overlay-bootstrap.conf` - Enables LwM2M bootstrap support

.. _build_lwm2m:

Building and Running
********************

.. |sample path| replace:: :file:`samples/nrf9160/lwm2m_client`

.. include:: /includes/build_and_run_nrf9160.txt


Queue Mode support
==================

To use the LwM2M Client sample with LwM2M Queue Mode support, build it with the ``-DOVERLAY_CONFIG=overlay-queue.conf`` option.
See :ref:`cmake_options` for instructions on how to add this option.

.. code-block:: console

   west build -b nrf9160dk_nrf9160_ns -- -DOVERLAY_CONFIG=-DOVERLAY_CONFIG=overlay-queue.conf

Bootstrap support
=================

To build the LwM2M Client sample with LwM2M bootstrap support, build it with the ``-DOVERLAY_CONFIG=overlay-bootstrap.conf`` option.
See :ref:`cmake_options` for instructions on how to add this option.
Keep in mind that the used bootstrap port is set in the aforementioned configuration file.

To successfully run the bootstrap procedure, the device must be first registered in the LwM2M bootstrap server.

The following instructions describe how to register your device and these instructions are specific to `public Leshan Bootstrap Server Demo`_:

1. Open the `Leshan Boostrap Server Demo web UI <public Leshan Bootstrap Server Demo_>`_.
#. In the :guilabel:`BOOTSTRAP` tab, click on :guilabel:`ADD CLIENTS CONFIGURATION`.
#. Enter your Client Endpoint name - nrf-*your device IMEI*
#. Press :guilabel:`NEXT` and in the :guilabel:`LWM2M Server Configuration` section, enter the following data:

   * Server URL - ``coaps://leshan.eclipseprojects.io:5684``
   * Select :guilabel:`Pre-shared Key` as the :guilabel:`Security Mode`
   * Identity - nrf-*your device IMEI*
   * Key - ``000102030405060708090a0b0c0d0e0f``

#. Press :guilabel:`NEXT` and in the :guilabel:`LWM2M Bootstrap Server Configuration` section enter the following data:

   * Server URL - ``coaps://leshan.eclipseprojects.io:5784``
   * Select :guilabel:`Pre-shared Key` as the :guilabel:`Security Mode`
   * Identity - nrf-*your device IMEI*
   * Key - ``000102030405060708090a0b0c0d0e0f``

   If you choose :guilabel:`Pre-shared Key`, add the values for :guilabel:`Identity` and :guilabel:`Key` fields (the configured Identity or Key need not match the Bootstrap Server configuration).
   The same credentials will be provided in the :guilabel:`Leshan Demo Server Security configuration` page (see :ref:`dtls_support` for instructions).
   If :guilabel:`No Security` is chosen, no further configuration is needed.
   In this mode, no DTLS will be used for the communication with the LwM2M server.
   Note that the :guilabel:`Client Configuration` page of the LWM2M Bootstrap server and the :guilabel:`Registered Clients` of the LWM2M server display only a limited number of devices by default.
   You can increase the number of displayed devices from the drop-down menu associated with :guilabel:`Rows per page`.
   In both cases, the menu is displayed at the bottom-right corner of the :guilabel:`Client Configuration` pages.
#. Build and run the LwM2M Client sample:

   .. code-block:: console

      west build -b nrf9160dk_nrf9160_ns -- -DOVERLAY_CONFIG=-DOVERLAY_CONFIG=overlay-bootstrap.conf

Testing
=======

|test_sample|

#. |connect_kit|
#. |connect_terminal|
#. Observe that the sample starts and automatically connects to the LwM2M Server with an endpoint named nrf-*your Device IMEI*.

.. note::
   The IMEI of your device can be found on the bottom of the nRF9160 DK near a bar code with the FCC ID at the bottom.

Sample output
=============

The following output is logged in the terminal:

.. code-block:: console

   *** Booting Zephyr OS build zephyr-v2.4.0-198-ga4ead9805140  ***
   [00:00:00.262,542] <inf> app_lwm2m_client: Run LWM2M client
   [00:00:00.263,214] <inf> app_lwm2m_client: Initializing modem.
   [00:00:00.277,648] <inf> app_lwm2m_client: endpoint: nrf-352656100162687
   [00:00:00.278,167] <dbg> app_lwm2m_loc.lwm2m_init_location: GPS device found: GPS_SIM
   [00:00:00.278,320] <err> app_lwm2m_temp: No temperature device found.
   [00:00:00.278,869] <inf> app_lwm2m_accel: accelerometer normal
   [00:00:00.278,961] <inf> app_lwm2m_firmware: Update Counter: current 0, update 0
   [00:00:00.278,991] <inf> app_lwm2m_firmware: Image is not confirmed OK
   [00:00:00.279,083] <inf> app_lwm2m_firmware: Marked image as OK
   [00:00:00.279,083] <dbg> app_lwm2m_firmware.lwm2m_init_image: Erased flash area 7
   [00:00:00.279,663] <inf> app_lwm2m_firmware: Update Counter updated
   [00:00:00.279,663] <inf> app_lwm2m_firmware: Firmware updated successfully
   [00:00:02.177,093] <inf> app_lwm2m_client: Connecting to LTE network.
   [00:00:02.177,093] <inf> app_lwm2m_client: This may take several minutes.
   [00:00:20.575,836] <inf> app_lwm2m_client: Connected to LTE network
   [00:00:20.577,117] <inf> net_lwm2m_rd_client: Start LWM2M Client: nrf-352656100162687
   [00:00:21.258,300] <inf> net_lwm2m_rd_client: RD Client started with endpoint 'nrf-352656100162687' with client lifetime 30
   [00:00:22.898,651] <dbg> app_lwm2m_client.rd_client_event: Registration complete
   [00:00:22.898,681] <inf> net_lwm2m_rd_client: Registration Done (EP='RMUcu4z1A2')


Dependencies
************

This application uses the following |NCS| libraries and drivers:

* :ref:`modem_info_readme`
* :ref:`at_cmd_parser_readme`
* ``drivers/gps_sim``
* ``drivers/sensor/sensor_sim``
* :ref:`dk_buttons_and_leds_readme`
* :ref:`lte_lc_readme`

It uses the following `sdk-nrfxlib`_ library:

* :ref:`nrfxlib:nrf_modem`

In addition, it uses the following sample:

* :ref:`secure_partition_manager`
