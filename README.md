# Nginx-uWSGI
Flask Applications with uWSGI and Nginx on CentOS and Ubuntu


#installation
sudo yum install epel-release
sudo yum install python-pip python-devel gcc nginx

#Virtualenv
sudo pip install virtualenv
mkdir ~/myproject
cd ~/myproject
virtualenv myprojectenv
source myprojectenv/bin/activate

#Flask and uwsgi
pip install uwsgi flask

vim ~/myproject/myproject.py
vim ~/myproject/wsgi.py
uwsgi --socket 0.0.0.0:8000 --protocol=http -w wsgi
deactivate
vim ~/myproject/myproject.ini
sudo systemctl start myproject
sudo systemctl enable myproject
sudo vim /etc/systemd/system/myproject.service
sudo vim /etc/nginx/nginx.conf
sudo usermod -a -G user nginx
chmod 710 /home/user
sudo nginx -t
sudo systemctl start nginx
sudo systemctl enable nginx
