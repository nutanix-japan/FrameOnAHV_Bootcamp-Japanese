.. _framegettingstarted:

----------------------
Getting Started
----------------------

Your User VLAN
++++++++++++++

Typically, Hosted POC clusters provide 2x /25 VLANs. In order to provide adequate IP space and support Frame lab requirements for this Bootcamp, each user has been assigned a UserXX-Network. 

   .. note:: Each User will have 10 IPs to use on each Network.

Throughout the Workshop there are multiple instances where you will need to substitute *XYZ* with the correct octet for your subnet, for example:

.. list-table::
   :widths: 25 35 40
   :header-rows: 1

   * - User Network
     - Start Address
     - End Address
   * - User01-Network
     - 10.42.\ *XYZ*\ .140
     - 10.42.\ *XYZ*\ .149
   * - User02-Network
     - 10.42.\ *XYZ*\ .150
     - 10.42.\ *XYZ*\ .159
   * - User03-Network
     - 10.42.\ *XYZ*\ .160
     - 10.42.\ *XYZ*\ .169
   * - User04-Network
     - 10.42.\ *XYZ*\ .170
     - 10.42.\ *XYZ*\ .179
   * - User05-Network
     - 10.42.\ *XYZ*\ .180
     - 10.42.\ *XYZ*\ .189
   * - User06-Network
     - 10.42.\ *XYZ*\ .190
     - 10.42.\ *XYZ*\ .199
   * - User07-Network
     - 10.42.\ *XYZ*\ .200
     - 10.42.\ *XYZ*\ .209
   * - User08-Network
     - 10.42.\ *XYZ*\ .210
     - 10.42.\ *XYZ*\ .219
   * - User09-Network
     - 10.42.\ *XYZ*\ .220
     - 10.42.\ *XYZ*\ .229
   * - User10-Network
     - 10.42.\ *XYZ*\ .230
     - 10.42.\ *XYZ*\ .239
   * - User11-Network
     - 10.42.\ *XYZ*\ .240
     - 10.42.\ *XYZ*\ .249
