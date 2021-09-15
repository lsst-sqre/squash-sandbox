squash-sandbox
==============

A `docker-compose`_ set up to run SQuaSH with the LSST stack locally. squash-sandbox can help you to test a change in the verify package, load metric definitions and specifications to SQuaSH, test dispatching verification jobs to SQuaSH, and play around with InfluxDB and Chronograf.

.. _docker-compose: https://docs.docker.com/compose/

Example 1: testing a change in the LSST verify package against the SQuaSH API
-----------------------------------------------------------------------------

Clone the verify repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

    git clone https://github.com/lsst/verify.git

Clone the squash-sandbox repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

    git clone https://github.com/lsst-sqre/squash-sandbox.git

Initialize the squash-sandbox services
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

    cd squash-sandbox
    docker-compose up -d

You can check the services running with ``docker-compose ps``.

Start the LSST stack container mounting the verify and the data directories inside the container
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

    docker-compose run --user root -v  $(pwd)/../verify:/home/lsst/verify -v $(pwd)/data:/home/lsst/data lsst /bin/bash

Load the LSST stack
^^^^^^^^^^^^^^^^^^^

.. code:: bash

    source /opt/lsst/software/stack/loadLSST.bash
    setup lsst_distrib


Checkout your ticket branch
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

    cd /home/lsst/verify
    git checkout your-ticket-branch

Build the verify package
^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

    setup -r .
    scons -Q -j 6 opt=3

You can now run dispatch_verify against the SQuaSH API
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

    dispatch_verify.py --url http://squash-api:5000/ --user squash --password squash --env jenkins --ignore-blobs --ignore-lsstsw /home/lsst/data/verify/Cfht_output_r.json

Note that the SQuaSH API user ``squash`` was already created during initialization.


Tear down the services
^^^^^^^^^^^^^^^^^^^^^^

First exit the container.
Then tear down the running services.

.. code:: bash

    exit
    docker-compose down

Example 2: loading metric definitions and specifications to SQuaSH
------------------------------------------------------------------

Example 3: Testing dispatch_verify options
------------------------------------------

Example 4: Creating dashboards in Chronograf
--------------------------------------------
