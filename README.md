filebeat
========

Ansible role which helps to install and configure Elastic Filebeat.

The configuration of the role is done in such way that it should not be necessary
to change the role for any kind of configuration. All can be done either by
changing role parameters or by declaring completely new configuration as a
variable. That makes this role absolutely universal. See the examples below for
more details.

Please report any issues or send PR.


Examples
--------

```yaml
---

- name: Example of how to install Filebeat with default configuration
  hosts: all
  roles:
    - filebeat

- name: Example of how to add new output
  hosts: all
  vars:
    # What to monitor
    filebeat_config_filebeat_inputs__custom:
      - type: log
        paths:
          - /var/log/httpd/*_log
        fields:
          app: apache
    # Add second output to Logstash
    filebeat_config_output__custom:
      logstash:
        index: filebeat
        hosts:
          - 127.0.0.1:5044
   roles:
    - filebeat

- name: Example of how to set output only to Logstash
  hosts: all
  vars:
    # What to monitor
    filebeat_config_filebeat_inputs__custom:
      - type: log
        paths:
          - /var/log/httpd/*_log
        fields:
          app: apache
    # Redefine the output to be only for Logstash
    filebeat_config_output:
      logstash:
        index: filebeat
        hosts:
          - 127.0.0.1:5044
   roles:
    - filebeat
```

This role doesn't allow to update the Filebeat index templates
(`/etc/filebeat/filebeat.template*.json`) for Elasticsearch. Please let me
know if such feature would be useful for you and I might implement it.


Role variables
--------------

```yaml
# Location of the config file
filebeat_config_file: /etc/filebeat/filebeat.yml

# YUM repo URL
filebeat_yum_repo_url: "{{ elastic_yum_repo_url | default('https://artifacts.elastic.co/packages/6.x/yum') }}"

# YUM repo GPG key
filebeat_yum_repo_key: "{{ elastic_yum_repo_key | default('https://artifacts.elastic.co/GPG-KEY-elasticsearch') }}"

# Extra YUM repo params
filebeat_yum_repo_params: "{{ elastic_yum_repo_params | default({}) }}"

# GPG key for the APT repo
filebeat_apt_repo_key: "{{ elastic_apt_repo_key | default('https://artifacts.elastic.co/GPG-KEY-elasticsearch') }}"

# APT repo string
filebeat_apt_repo_string: "{{ elastic_apt_repo_string | default('deb https://artifacts.elastic.co/packages/6.x/apt stable main') }}"

# Extra APT repo params
filebeat_apt_repo_params: "{{ elastic_apt_repo_params | default({}) }}"

# Package to be installed (explicit version can be specified here)
filebeat_pkg: filebeat

# Name of the service
filebeat_service: filebeat


# Default paths of the filebeat inputs
filebeat_config_filebeat_inputs_paths:
  - /var/log/*.log

# Default filebeat inputs
filebeat_config_filebeat_inputs__default:
  - type: log
    paths: "{{ filebeat_config_filebeat_inputs_paths }}"

# Custom filebeat inputs
filebeat_config_filebeat_inputs__custom: []

# Final filebeat inputs
filebeat_config_filebeat_inputs: "{{
  filebeat_config_filebeat_inputs__default +
  filebeat_config_filebeat_inputs__custom }}"


# Default paths of the filebeat
filebeat_config_filebeat__default:
  inputs: "{{ filebeat_config_filebeat_inputs }}"

# Custom filebeat
filebeat_config_filebeat__custom: {}

# Final filebeat
filebeat_config_filebeat: "{{
  filebeat_config_filebeat__default | combine(
  filebeat_config_filebeat__custom) }}"


# List of hosts of the output elasticsearch
filebeat_config_output_elasticsearch_hosts:
  - localhost:9200

# Default output elasticsearch
filebeat_config_output_elasticsearch__default:
  hosts: "{{ filebeat_config_output_elasticsearch_hosts }}"

# Custom output elasticsearch
filebeat_config_output_elasticsearch__custom: {}

# Final output elasticsearch
filebeat_config_output_elasticsearch: "{{
  filebeat_config_output_elasticsearch__default | combine(
  filebeat_config_output_elasticsearch__custom) }}"


# Default output
filebeat_config_output__default:
  elasticsearch: "{{ filebeat_config_output_elasticsearch }}"

# Custom output
filebeat_config_output__custom: {}

# Final output
filebeat_config_output: "{{
  filebeat_config_output__default | combine(
  filebeat_config_output__custom) }}"


# Default configuration
filebeat_config__default:
  filebeat: "{{ filebeat_config_filebeat }}"
  output: "{{ filebeat_config_output }}"

# Custom configuration
filebeat_config__custom: {}

# Final configuration
filebeat_config: "{{
  filebeat_config__default | combine(
  filebeat_config__custom) }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`elasticsearch`](https://github.com/jtyr/ansible-elasticsearch) (optional)
- [`kibana`](https://github.com/jtyr/ansible-kibana) (optional)
- [`logstash`](https://github.com/jtyr/ansible-logstash) (optional)


License
-------

MIT


Author
------

Jiri Tyr
