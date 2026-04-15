python3 -m venv .venv
source .venv/bin/activate
pip install ansible kubernetes



Deploy di un sistema di monitoraggio attraverso Podman
=========

Utilizzeremo un ruolo ansible per fare deploy di un sistema di monitoraggio con Haproxy Load Balancer, Grafana e Prometheus per il monitoraggio 

Requirements
------------

Utilizzare un sistema operativo MacOS con chipset Mac ARM, avere Homebrew ed Ansible 2.20.4 installato.
Installare le dependencies per kubernetes attraverso pip:
python3 -m venv venv
source venv/bin/activate
pip3 install kubernetes pyyaml jsonpatch



Dependencies
------------

Le uniche dipendenze che si necessitano sono:

  community.general (per i comandi homebrew)
  containers.podman (per i comandi di deploy container)
  kubernetes.core

Nel file requirements.yml sono presenti i nomi

License
-------

BSD

Author Information
------------------

Daniele Bottino from Sourcesense , second module
