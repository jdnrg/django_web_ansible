About
=====
Integrated automation management platform ~ integrated ansibleUI

Function
=====
* Organize the layout according to the project, which is more intuitive and easy to use
* Provide easy-to-understand host management interface
* Provide user key management function
* Provide YML file interface management function
* Provide task deployment function
* Provide file transfer function
* Provide command execution function
* Provide appointment execution function
* Provide task template function
* Provide log function
* Provide email notification function
* Task distribution based on celery queue for easy expansion

UserManual
=====
[ansible_ui platform user manual] (https://github.com/yianjiajia/django_web_ansible/tree/master/documents)

Requirements
=====
* pip
* virtualenv
* mysql-server, mysql-devel
* openldap-devel

Install
=====
* The system is CentOS6.5
* Add system user

    
        useradd ansible
        su-ansible


* Configure virtualenv environment


        virtualenv envansible
        source envansible / bin / active




* Install dependent libraries


        cd ansible-ui
        pip install -r requirements.txt
        pip install PIL --allow-external PIL --allow-unverified PIL
    

* Configure ldap, database and mail information


        cd desktop / core / internal
        vim settings_local.py
        # Modify LDAP Datebase Mail and ansible_playbook command location (which ansible_playbook) configuration
        If you need to use ldap, you also need to modify settings.py and uncomment the following line
        # 'desktop.core.auth.backend.LdapBackend',


* Configuration database


        create database ansible CHARACTER SET utf8;
        grant all on ansible. * to ansibleuser @ 'localhost' identified by '******';


* Initialize the database

        python manage.py schemamigration desktop.apps.account --init
        python manage.py schemamigration desktop.apps.ansible --init
        python manage.py syncdb
        python manage.py migrate ansible
        python manage.py migrate account
        python manage.py migrate kombu.transport.django
        python manage.py migrate djcelery
        python manage.py migrate guardian


* Configure celery


        Modify celery-conf / supervisord.conf
        [inet_http_server] #Configure web management supervisor
        [program: ansible_celeryd] #Modify virtualenv and ansible_ui home in command


* Launch celery


        supervisord -c celery-conf / supervisord.conf


* Configure ansible


        cp ansible-conf / ansible.cfg ~ / .ansible.cfg


* Vagrant + Ansible

    Thanks to [yunlzheng] (https://github.com/yunlzheng) for providing a way to automatically build a development environment using vagrant + ansible [Vagrantfile] (https://github.com/alaxli/ansible_ui/blob/master/Vagrantfile) + [playbook.yml] (https://github.com/alaxli/ansible_ui/blob/master/playbook.yml), the specific operation is recommended to read [《Using Ansible to integrate the development environment into version management ”] (http: // yunlzheng.github.io/2014/08/08/vagrant-with-ansible/)


Run
=====
* Direct operation


        python manage.py runserver ip: 8000


* apache + wsgi

        Modify apache-conf / ansible.cfg: ansible_ui_dir to point to the actual directory
        Modify django.wsgi: yourvirtualenv points to the actual directory
        Copy apache-conf / ansible.cfg to the apache configuration directory
        Restart httpd




Problem
=====
* Known issue: Task cannot run on single-core CPU server ("ValueError: I / O operation on closed file" error will be reported when calling pexpect, similar to this [problem] (http://stackoverflow.com/questions/ 24524162 / pexpect-runs-failed-when-use-multiprocessing), if you want to run on a single-core CPU server, please reduce pexpect to version 2.4
