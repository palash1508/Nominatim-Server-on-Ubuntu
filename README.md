# Nominatim-Server-on-Ubuntu

**#Ubuntu must be updated to 22.04**

**userName should be replaced by userprofile**

**#Below code are for installing required packages**

**#Run these commands in terminal**

sudo apt install -y python3-behave
sudo apt install -y php-codesniffer
sudo apt install -y phpunit
sudo apt install -y pylint
sudo python38 -m pip install -y python-dotenv
sudo apt install -y osm2pgsql
sudo apt  install -y cmake
sudo snap install -y cmake 
sudo apt install -y python3-pip
sudo apt install -y acl
sudo pip install -U pytest
sudo apt install -y php-cgi unzip recode
sudo apt install -y apache2 libapache2-mod-php
sudo apt install -y net-tools
sudo apt-get install -y osmctools
sudo apt install -y php-cgi
sudo apt install -y build-essential cmake g++ libboost-dev libboost-system-dev \
                    libboost-filesystem-dev libexpat1-dev zlib1g-dev \
                    libbz2-dev libpq-dev liblua5.3-dev lua5.3 \
                    postgresql-server-dev-14 postgresql-14-postgis-3 \
                    postgresql-contrib-14 postgresql-14-postgis-3-scripts \
                    php-cli php-pgsql php-intl libicu-dev python3-dotenv \
                    python3-psycopg2 python3-psutil python3-jinja2 \
                    python3-icu python3-datrie



**#run below command for Creating a new system user named "nominatim" with a home directory of "/srv/nominatim"**

sudo useradd -d /srv/nominatim -s /bin/bash -m nominatim



**#run below command for Setting environment variables for the username and home directory of the nominatim user.**

export USERNAME=nominatim

export USERHOME=/srv/nominatim



**#open the sudoers for configuration**

visudo



**#Add these entries to the sudoers for granting various privileges**

%userName ALL=(ALL) ALL

%nominatim ALL=(ALL)  ALL

%postgres ALL=(ALL)  ALL



**#copy Paste the code from here**

sudo chmod a+x $USERHOME
sudo setfacl -R -m u:postgres:rwx /srv/nominatim/
sudo systemctl restart postgresql
sudo -u postgres -i
sudo -u postgres createuser -s $USERNAME
sudo -u postgres createuser www-data
su username
sudo -i
export USERNAME=nominatim
export USERHOME=/srv/nominatim

cd $USERHOME
wget https://nominatim.org/release/Nominatim-4.2.0.tar.bz2
tar xf Nominatim-4.2.0.tar.bz2

mkdir build
cd build
sudo cmake /srv/nominatim/Nominatim-4.2.0
sudo make
sudo make install

cd ..
mkdir /srv/nominatim/nominatim-project
mkdir /srv/nominatim/nominatim-project/website

sudo setfacl -R -m u:nominatim:rwx /srv/nominatim/
sudo setfacl -R -m u:postgres:rwx /srv/nominatim/

mkdir nominatim-planet
cd /srv/nominatim/nominatim-planet
export PROJECT_DIR=/srv/nominatim/nominatim-planet

cd $PROJECT_DIR

wget https://www.nominatim.org/data/wikimedia-importance.sql.gz

wget https://download.geofabrik.de/asia/india-latest.osm.pbf

**#upto here**



**#open postgres user**

sudo -u postgres -i


**#open postgresql.conf file and make some updates**

nano /etc/postgresql/14/main/postgresql.conf


**#make these changes in the file**

listen_addresses = 'localhost'
shared_buffers = 2GB
maintenance_work_mem = 10GB
autovacuum_work_mem = 2GB
work_mem = 50MB
effective_cache_size = 24GB
synchronous_commit = off
max_wal_size = 1GB
checkpoint_timeout = 10min
checkpoint_completion_target = 0.9




**#copy paste below command from here**

su userName
sudo -i
sudo systemctl restart postgresql
sudo -u postgres -i
cd
nominatim import --osm-file /srv/nominatim/nominatim-planet/india-latest.osm.pbf 2>&1 | tee setup.log
nominatim admin --check-database
nominatim import --continue indexing
nominatim admin --check-database
cd /srv/nominatim/nominatim-planet/
nominatim refresh --wiki-data --importance 2>&1 | tee setup.log
nominatim admin --check-database
cp -r /var/lib/postgresql/website/ /srv/nominatim/nominatim-project/
su userName
sudo -i
sudo tee /etc/apache2/conf-available/nominatim.conf << EOFAPACHECONF
<Directory "/srv/nominatim/nominatim-project/website">
  Options FollowSymLinks MultiViews
  AddType text/html   .php
  DirectoryIndex search.php
  Require all granted
</Directory>
Alias /nominatim /srv/nominatim/nominatim-project/website
EOFAPACHECONF


**#open postgres user**

sudo -u postgres -i

**#run these following command in postgres user**

a2enconf nominatim
systemctl restart apache2
systemctl status apache2
systemctl restart postgresql
nominatim serve


**#open browser and check the server running properly** 

run-: localhost/nominatim => OK
localhost/nominatim/reverse.php?format=json&lat=27.1750090510034&lon=78.04209025
