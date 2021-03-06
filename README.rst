========
logstash
========

Install and configure a Logstash.

.. note::

    See the full `Salt Formulas installation and usage instructions
    <http://docs.saltstack.com/en/latest/topics/development/conventions/formulas.html>`_.

Available states
================

.. contents::
    :local:

``logstash``
------------

It executes ``repository``, ``install``, ``config``, ``patterns``,
``sysconfig``, ``jvmoptions`` and ``service`` states.

``logstash.repository``
-----------------------

It adds the Elastic APT/YUM repository.

``logstash.install``
--------------------

It installs the latest version of Logstash from APT/YUM repository.

``logstash.config``
-------------------

It configures Logstash by reading the global configuration from
``logstash:config`` pillar key. Input, filtering and oupt plugins
configuration can be loaded from files defined under ``logstash:input``,
``logstash.filter`` and ``logstash:output`` keys.

.. code:: yaml

    logstash:
        ...

        config:
            queue.type: persisted
            queue.max_bytes: 4gb

        input:
            00kafka: salt://path/to/kafka.conf
            01syslog: salt://path/to/syslog.conf

        filter:
            00grok: salt://path/to/grok.sls

        output:
            00elasticsearch: salt://path/to/elasticsearch.conf
            01s3: salt://path/to/s3.conf

        ...

``logstash.patterns``
---------------------

It defines the custom Grok patterns declared under ``logstash:patterns``
pillar key. For each patterns group, a file named
``/etc/logstash/patterns/<GROUPNAME>`` is created.

.. code:: yaml

    logstash:
        ...
        patterns:
            syslog:
                - 'SYSLOGBASE %{SYSLOGTIMESTAMP:timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?: %{GREEDYDATA:message}'
            salt:
                - 'SALTLOG_LEVEL (?:DEBUG|FATAL|ERROR|WARNING|INFO)'
                - 'SALTCMD @?[a-z]\w+(?:\.@?[a-z]\w+)*'
                - 'SALTLOGBASE %{TIMESTAMP_ISO8601:timestamp} \[%{SALTCMD:saltcmd}(?:([ \t]+)?)\]\[%{SALTLOG_LEVEL:priority}(?:[ \t]+)\] %{GREEDYDATA:message}'
        ...

``logstash.sysconfig``
----------------------

It populates the environment configuration file loaded by ``Systemd``.

``logstash.jvmoptions``
-----------------------

It sets the JVM options under ``/etc/logstash/jvm.options``.

``logstash.service``
--------------------

It enables and run the Logstash ``Systemd`` unit

References
==========

-  `Logstash Documentation <https://www.elastic.co/guide/en/logstash/current/index.html>`__
-  `Salt Formulas <https://docs.saltstack.com/en/latest/topics/development/conventions/formulas.html>`__
