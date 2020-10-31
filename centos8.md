Install the Components from the CentOS and EPEL Repositories
Our first step will be to install all of the pieces that we need from the repositories. We will need to add the EPEL repository, which contains some extra packages, in order to install some of the components we need.
You can enable the EPEL repo by typing:
sudo yum install epel-release
Once access to the EPEL repository is configured on our system, we can begin installing the packages we need. We will install pip, the Python package manager, in order to install and manage our Python components. We will also get a compiler and the Python development files needed to build uWSGI. We’ll install Nginx now as well.
You can install all of these components by typing:
sudo yum install python-pip python-devel gcc nginx
