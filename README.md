# Nginx-uWSGI
Flask Applications with uWSGI and Nginx on CentOS and Ubuntu

# Centos 8.2
installation of required libraries 

      sudo yum install epel-release      
      sudo yum install python-pip python-devel gcc nginx
      sudo pip install virtualenv

Creating and activating Virtual environment
      mkdir ~/myproject
      cd ~/myproject
      virtualenv myprojectenv
      source myprojectenv/bin/activate

Installing the required libraries inside the Virtualenv (Flask and uwsgi).
pip install uwsgi flask

vim ~/myproject/myproject.py
              from flask import Flask
              application = Flask(__name__)

              @application.route("/")
              def hello():
                  return "<h1 style='color:blue'>Hello There!</h1>"

              if __name__ == "__main__":
                  application.run(host='0.0.0.0')
vim ~/myproject/wsgi.py
              from myproject import application

              if __name__ == "__main__":
                  application.run()
uwsgi --socket 0.0.0.0:8000 --protocol=http -w wsgi
deactivate
vim ~/myproject/myproject.ini
sudo systemctl start myproject
sudo systemctl enable myproject
sudo vim /etc/systemd/system/myproject.service
              [Unit]
              Description=uWSGI instance to serve myproject
              After=network.target

              [Service]
              User=user
              Group=nginx
              WorkingDirectory=/home/user/myproject
              Environment="PATH=/home/user/myproject/myprojectenv/bin"
              ExecStart=/home/user/myproject/myprojectenv/bin/uwsgi --ini myproject.ini

              [Install]
              WantedBy=multi-user.target
sudo vim /etc/nginx/nginx.conf
              server {
                listen 80;
                server_name server_domain_or_IP;

                location / {
                    include uwsgi_params;
                    uwsgi_pass unix:/home/user/myproject/myproject.sock;
                }
            }
sudo usermod -a -G user nginx
chmod 710 /home/user
sudo nginx -t
sudo systemctl start nginx
sudo systemctl enable nginx



You need to disable SELinux or add a rule for nginx SELinux to workaround the permission denied/502 Bad Gateway issue.

Here are the steps:

If you are getting 502 Bad Gateway, then check logs cat /var/logs/nginx/error.logs
I was getting something like (13: Permission denied) while connecting to upstream ...
Check the status of SELinux using sestatus
If it says enabled, vi into /etc/sysconfig/selinux. This is a symlink to /etc/selinux/config so modify this file in case you don’t find the above file. Terminal command: sudo vi /etc/sysconfig/selinux
The file is highly self-explanatory. Just change the value of SELINUX to disabled
Most important step - REBOOT! sudo reboot



disabling SELinux is not always an option, especially when running an app on a openly available server.
Should you have the annoying issue with nginx and its Bad Gateway 502 error, you might want to allow connections through httpd. To accomplish that without turning off SELinux you can run the following command:

sudo semanage permissive -a httpd_t
This will allow nginx to manage connections without disabling SELinux (source).
As mentioned above, restart the services, or better reboot the server to allow changes to take effect.
Hope to be helpful!



link : https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uwsgi-and-nginx-on-centos-7
