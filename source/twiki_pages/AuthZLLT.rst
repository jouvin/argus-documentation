.. _argus-load-lifetime-testing:

Authorization Service: Load and Lifetime Testing
================================================

It is important to perform load and lifetime tests on any service being
put in to production. Load tests are used to determine how the service
behaves, and eventually fails, under load while lifetime tests determine
how the service behaves over a prolonged period of time with average
load.

Note, this testing framework used here was actually developed by the
`Shibboleth <http://shibboleth.internet2.edu>`__ team for testing their
software but it allows other test packs to be installed so it can also
be used in testing this service.

Testing Framework Basics
------------------------

Requirements
~~~~~~~~~~~~

-  Unix OS based machine(s)
-  `Subversion <http://subversion.tigris.org/>`__ client (necessary in
   order to download the framework)
-  Java 1.5

Downloading and Installing the Framework
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Checkout the base testing framework. The resulting
   ``grinder-framework`` directory will be known a ``GF_HOME``
   throughout the rest of this document.

   -
      ``svn co https://svn.middleware.georgetown.edu/shib-extension/java-loadtest/trunk/grinder-framework grinder-framework``

#. Within \_GF\ *HOME/test* checkout the Argus test packs

   -  ``svn co http://svnweb.cern.ch/guest/glxa/loadtests/pepcli-authz pepcli-authz``
   -  ``svn co http://svnweb.cern.ch/guest/glxa/loadtests/pepcli-multiauthz pepcli-multiauthz``
   -  ``svn co http://svnweb.cern.ch/guest/glxa/loadtests/pepj-authz pepj-authz``

Installation is now complete.

Running the Framework
~~~~~~~~~~~~~~~~~~~~~

The load testing framework is composed of two parts, an agent and a
console. The agent performs the actual work while the console, a GUI
application, collects the results from agents and displays some metrics
based on those results

To run the console execute the ``GF_HOME/bin/console.sh`` command. This
will start the console which will listen on port 6372 for agent results.
Be sure that this port is accessible to the agents you start.

To run the console execute the ``GF_HOME/bin/agent.sh test_name``
command. The name of the test is dependent on which test you will run
and is given in the documentation for those tests. You can install
agents on more than one machine and have them report back to a single
client. This is useful if one agent machine is not capable of generating
enough load.

Common Configuration Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Each test package contains a ``test.properties`` file that contains
configuration information for the given test. The later half of these
options are Grinder specific properties. In most cases you'll need to
change the following properties.

+---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Property Name       | Value                                                                                                                                                                                                                            |
+=====================+==================================================================================================================================================================================================================================+
| grinder.consoleHost | The IP address of the machine on which the console is running                                                                                                                                                                    |
+---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| grinder.processes   | Number of grinder processes to run. Should be equal to the number of CPU cores on the agent machine.                                                                                                                             |
+---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| grinder.threads     | Number of unique clients, per process, that will connect to the service                                                                                                                                                          |
+---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| grinder.runs        | Number of times each client will connect to the service, 0 (zero) means to keep going until the console indicates the test should be stopped. Using a value of 1 (one) is useful for testing that your configuration is correct. |
+---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

PEPCLI Test
-----------

This test uses the `C language PEP
CLI <AuthZPEP#PEP_C_Policy_Enforcement_Point_C>`__ to issue basic
authorization requests. It a good test to run if you are trying get
measurements for applications which do not maintain state between
requests (e.g. a new client is created for every request).

**Test Name:** pepcli-authz

Configuration Options
~~~~~~~~~~~~~~~~~~~~~

+--------------------------+----------------------------------------------------------------------------------------------+--------------------+
| Property Name            | Value                                                                                        | Required           |
+==========================+==============================================================================================+====================+
| pepcli.bin               | The path to the pepcli binary.                                                               | Yes                |
+--------------------------+----------------------------------------------------------------------------------------------+--------------------+
| pepcli.pepds             | A space separated list of PEPd endpoints                                                     | Yes                |
+--------------------------+----------------------------------------------------------------------------------------------+--------------------+
| pepcli.certchain         | Path, relative to ``GF_HOME``, to the certchain used to identify the subject or the request  | Yes                |
+--------------------------+----------------------------------------------------------------------------------------------+--------------------+
| pepcli.resourceid        | The resource ID used within the request                                                      | One is required.   |
+--------------------------+----------------------------------------------------------------------------------------------+--------------------+
| pepcli.resourceid.prefix | Prefix added to the *grinder-agent-name* to create the resource ID used in the request       | ^                  |
+--------------------------+----------------------------------------------------------------------------------------------+--------------------+
| pepcli.actionid          | The action ID used within the request                                                        | One is required.   |
+--------------------------+----------------------------------------------------------------------------------------------+--------------------+
| pepcli.actionid.prefix   | Prefix added to the *grinder-agent-run-number* to create the resource ID used in the request | ^                  |
+--------------------------+----------------------------------------------------------------------------------------------+--------------------+
| pepcli.timeout           | The connection timeout in seconds                                                            | No, defaults to 30 |
+--------------------------+----------------------------------------------------------------------------------------------+--------------------+

PEPCLI Test
-----------

This test uses the `C language PEP
CLI <AuthZPEP#PEP_C_Policy_Enforcement_Point_C>`__ to issue multiple
basic authorization requests. It a good test to run if you are trying
get measurements for applications which do not maintain state between
requests (e.g. a new client is created for every request).

**Test Name:** pepcli-multiauthz

Configuration Options
~~~~~~~~~~~~~~~~~~~~~

+----------------+---------------------------------------------+--------------------+
| Property Name  | Value                                       | Required           |
+================+=============================================+====================+
| pepcli.bin     | The path to the pepcli binary.              | Yes                |
+----------------+---------------------------------------------+--------------------+
| pepcli.pepds   | A space separated list of PEPd endpoints    | Yes                |
+----------------+---------------------------------------------+--------------------+
| pepcli.timeout | The connection timeout in seconds           | No, defaults to 30 |
+----------------+---------------------------------------------+--------------------+
| pepcli.tests   | A set of test inputs. See below for format. | Yes                |
+----------------+---------------------------------------------+--------------------+

Test Input Format
~~~~~~~~~~~~~~~~~

The test import format is as follows:

::

    [
       ['ce1', 'submit', '/home/jsmith/.globus/usercert.pem', 'Permit'],
       ['ce2', 'submit', '/home/jsmith/.globus/usercert.pem', 'Deny'],
       ['ce3', 'submit', '/home/jsmith/.globus/usercert.pem', 'Not Applicable'],
    ]

The property value is contained in matching '[' and ']' brackets. Then
each test individual test scenario is contained with matching '[' and
']'. Each testing scenario has four comma-separated values: the resource
ID, action ID, and certificate chain used in the request and then the
expected outcome. Each test scenario is also comma-separated. The use of
the '\\' character can be used to indicate that the property value
continues on to the next line.

PEP-J Test
----------

This test uses the `Java language PEP
CLI <AuthZPEP#PEP_J_Policy_Enforcement_Point_C>`__ to issue
authorization requests. It is a good test to run if you are trying to
get measurements for application which maintain state between requests
(e.g. a single client is used for multiple requests).

**Test Name:** pepj-authz

Configuration Option
~~~~~~~~~~~~~~~~~~~~


   ===================   ==================================================================================   ========
   Property Name          Value                                                                               Required
   ===================   ==================================================================================   ========
   authz.client.config   Path to the client configuration file. Relative paths are relative to ``GF_HOME``.   Yes
   ===================   ==================================================================================   ========
