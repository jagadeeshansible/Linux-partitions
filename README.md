Linux partitions
=========

creating linux partitions using parted module and mount with uuid's

Linux Base os
------------

using RHEL8

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

   ---
# tasks file for partitions
    - name: Creating partitions
      parted:
        device: "{{ item.device }}"
        number: "{{ item.number }}"
        state: "{{ item.state }}"
        part_start: "{{ item.part_start }}"
        part_end: "{{ item.part_end }}"
      loop: "{{ partions }}"
      register: parted
      
    - name: Creating Filesystem
      filesystem:
        dev: "{{ item.dev }}"
        fstype: "{{ item.fstype }}"
        force: yes
      loop: "{{ filesystem }}"
      register: fs
      when: parted is success
      
    - name: Refresing facts to collect newly generated UUID's
      setup:
      
    - name:Gathering UUID's and passed into variables
      set_fact:
        uuid_1: "{{ ansible_facts['devices']['nvme0n2']['partitions']['nvme0n2p1']['uuid'] }}"
        uuid_2: "{{ ansible_facts['devices']['nvme0n2']['partitions']['nvme0n2p2']['uuid'] }}"
    
    - name: creainng mount points
      mount:
        path: "{{ item.path }}"
        src: "UUID={{ item.src }}"
        fstype: "{{ item.fstype }}"
        state: "{{ item.state }}"
      loop:
        - { path: /folder-xfs, src: "{{ uuid_1 }}", fstype: xfs, state: mounted }
        - { path: /folder-ext4, src: "{{ uuid_2 }}", fstype: ext4, state: mounted }
       
      register: mp
      when: fs is success
                           

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
