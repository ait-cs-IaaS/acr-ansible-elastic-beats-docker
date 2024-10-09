# Ansible-Role: acr-ansible-elastic-beats-docker 

AIT-CyberRange: Installs and configures elastic beats in a docker container. 


## Requirements

- Debian or Ubuntu

## Role Variables

```yaml
beat_playbook: "{{ beat | upper }}"

beat_name: auditbeat
beat_version: 7.9.3

beat_conf: {}
beat_output_conf:
  output: '{{ output_conf }}'
beat_logging_conf:
  logging: '{{ logging_conf }}'

logging_conf: {}
output_conf: {}

beat_docker_name: '{{ beat_name }}'
beat_docker_hostname: '{{ dns_hostname }}'
beat_docker_image: docker.elastic.co/beats/{{ beat_name }}:{{ beat_version }}
beat_docker_extra_args: >-
  {% if beat == 'auditbeat' %}
  --volume=/proc:/hostfs/proc:ro
  --volume=/sys/fs/cgroup:/hostfs/sys/fs/cgroup:ro
  --volume=/:/hostfs:ro
  --volume=/sys/kernel/tracing:/sys/kernel/tracing:rw
  --volume=/sys/kernel/debug:/sys/kernel/debug:ro
  {% elif beat == 'metricbeat' %}
  --volume="/sys/fs/cgroup:/hostfs/sys/fs/cgroup:ro"
  --volume="/proc:/hostfs/proc:ro"
  --volume="/:/hostfs:ro"
  {% elif beat == 'filebeat' %}
  --volume=/var/lib/docker/containers:/var/lib/docker/containers:ro
  --volume=/var/log:/var/log:rw
  {% endif %}

beat_modules: []

beat_ca_crt: /usr/local/share/ca-certificates/ca.crt
```

## Example Playbook

```yaml
- hosts: all
  roles:
    - acr-ansible-elastic-beats-docker 
      vars:
        beat: filebeat
        beat_conf: "{{ filebeat_beat_conf }}"
        output_conf: "{{ beats_output_conf }}"
        logging_conf: "{{ filebeat_logging_conf}}"
        elastic_beats_modules: "{{ filebeat_modules }}"
```

## License

GPL-3.0

## Author

- Lenhard Reuter