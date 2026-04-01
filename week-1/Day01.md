# Day 01 — Basics

## 🔹 Ansible Task — Install Nginx on Pathnex Server

Rewrite this YAML manually:

```yaml
- name: Install Nginx on Pathnex server
  hosts: all
  become: yes

  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present
