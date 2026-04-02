# Day 01 — Basics

## 🔹 Ansible Task — Install Nginx on HireReady Server

Rewrite this YAML manually:

```yaml
- name: Install Nginx on HireReady server
  hosts: all
  become: yes

  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present
