# Nominatim-Server-on-Ubuntu

**#Ubuntu must be updated to 22.04**

***%userName should be replaced by userprofile%***

**#Below code are for installing required packages**

**#Run these commands in terminal**



<sub>sudo apt install -y python3-behave</sub>

<sub>sudo apt install -y php-codesniffer</sub>

<sub>sudo apt install -y phpunit</sub>

<sub>sudo apt install -y pylint</sub>

<sub>sudo python38 -m pip install -y python-dotenv</sub>

<sub>sudo apt install -y osm2pgsql</sub>

<sub>sudo apt  install -y cmake</sub>

<sub>sudo snap install -y cmake </sub>

<sub>sudo apt install -y python3-pip</sub>

<sub>sudo apt install -y acl</sub>

<sub>sudo pip install -U pytest</sub>

<sub>sudo apt install -y php-cgi unzip recode</sub>

<sub>sudo apt install -y apache2 libapache2-mod-php</sub>

<sub>sudo apt install -y net-tools</sub>

<sub>sudo apt-get install -y osmctools</sub>

<sub>sudo apt install -y php-cgi</sub>

<sub>sudo apt install -y build-essential cmake g++ libboost-dev libboost-system-dev \
                        libboost-filesystem-dev libexpat1-dev zlib1g-dev \
                        libbz2-dev libpq-dev liblua5.3-dev lua5.3 \
                        postgresql-server-dev-14 postgresql-14-postgis-3 \
                        postgresql-contrib-14 postgresql-14-postgis-3-scripts \
                        php-cli php-pgsql php-intl libicu-dev python3-dotenv \
                        python3-psycopg2 python3-psutil python3-jinja2 \
                        python3-icu python3-datrie</sub>



**#run below command for Creating a new system user named "nominatim" with a home directory of "/srv/nominatim"**

<sub>sudo useradd -d /srv/nominatim -s /bin/bash -m nominatim</sub>



**#run below command for Setting environment variables for the username and home directory of the nominatim user.**

<sub>export USERNAME=nominatim</sub>

<sub>export USERHOME=/srv/nominatim</sub>



**#open the sudoers for configuration**

<sub>visudo</sub>



**#Add these entries to the sudoers for granting various privileges**

<sub>%userName ALL=(ALL) ALL</sub>

<sub>%nominatim ALL=(ALL)  ALL</sub>

<sub>%postgres ALL=(ALL)  ALL</sub>



**#copy Paste the code from here**

<sub>sudo chmod a+x $USERHOME</sub>

<sub>sudo setfacl -R -m u:postgres:rwx /srv/nominatim/</sub>

<sub>sudo systemctl restart postgresql</sub>

<sub>sudo -u postgres -i</sub>

<sub>sudo -u postgres createuser -s $USERNAME</sub>

<sub>sudo -u postgres createuser www-data</sub>

<sub>su username</sub>

<sub>sudo -i</sub>

<sub>export USERNAME=nominatim</sub>

<sub>export USERHOME=/srv/nominatim</sub>

<sub>cd $USERHOME</sub>

<sub>wget https://nominatim.org/release/Nominatim-4.2.0.tar.bz2</sub>

<sub>tar xf Nominatim-4.2.0.tar.bz2</sub>


<sub>mkdir build</sub>

<sub>cd build</sub>

<sub>sudo cmake /srv/nominatim/Nominatim-4.2.0</sub>

<sub>sudo make</sub>

<sub>sudo make install</sub>

<sub>cd ..</sub>

<sub>mkdir /srv/nominatim/nominatim-project</sub>

<sub>mkdir /srv/nominatim/nominatim-project/website</sub>


<sub>sudo setfacl -R -m u:nominatim:rwx /srv/nominatim/</sub>

<sub>sudo setfacl -R -m u:postgres:rwx /srv/nominatim/</sub>

<sub>mkdir nominatim-planet</sub>

<sub>cd /srv/nominatim/nominatim-planet</sub>

<sub>export PROJECT_DIR=/srv/nominatim/nominatim-planet</sub>

<sub>cd $PROJECT_DIR</sub>

<sub>wget https://www.nominatim.org/data/wikimedia-importance.sql.gz</sub>

<sub>wget https://download.geofabrik.de/asia/india-latest.osm.pbf</sub>

**#upto here**



**#open postgres user**

<sub>sudo -u postgres -i</sub>


**#open postgresql.conf file and make some updates**

<sub>nano /etc/postgresql/14/main/postgresql.conf</sub>


**#make these changes in the file**

<sub>listen_addresses = 'localhost'</sub>

<sub>shared_buffers = 2GB</sub>

<sub>maintenance_work_mem = 10GB</sub>

<sub>autovacuum_work_mem = 2GB</sub>

<sub>work_mem = 50MB</sub>

<sub>effective_cache_size = 24GB</sub>

<sub>synchronous_commit = off</sub>

<sub>max_wal_size = 1GB</sub>

<sub>checkpoint_timeout = 10min

  <sub>checkpoint_completion_target = 0.9</sub>




**#copy paste below command from here**

  <sub>su userName</sub>

  <sub>sudo -i</sub>

  <sub>sudo systemctl restart postgresql</sub>

  <sub>sudo -u postgres -i</sub>

<sub>cd

<sub>nominatim import --osm-file /srv/nominatim/nominatim-planet/india-latest.osm.pbf 2>&1 | tee setup.log</sub>

  <sub>nominatim admin --check-database</sub>

  <sub>nominatim import --continue indexing</sub>

  <sub>nominatim admin --check-database</sub>

  <sub>cd /srv/nominatim/nominatim-planet/</sub>

  <sub>nominatim refresh --wiki-data --importance 2>&1 | tee setup.log</sub>

  <sub>nominatim admin --check-database</sub>

  <sub>cp -r /var/lib/postgresql/website/ /srv/nominatim/nominatim-project/</sub>

  <sub>su userName</sub>

  <sub>sudo -i</sub>

  <sub>sudo tee /etc/apache2/conf-available/nominatim.conf << EOFAPACHECONF</sub>

<sub><Directory "/srv/nominatim/nominatim-project/website"></sub>

  <sub>Options FollowSymLinks MultiViews</sub>
  
  <sub>AddType text/html   .php</sub>
  
 <sub> DirectoryIndex search.php</sub>
  
 <sub> Require all granted</sub>
  
<sub></Directory></sub>

<sub>Alias /nominatim /srv/nominatim/nominatim-project/website</sub>

 <sub> EOFAPACHECONF</sub>

**#upto here**




**#open postgres user**

  <sub>sudo -u postgres -i</sub>

**#run these following command in postgres user**

  <sub>a2enconf nominatim</sub>

  <sub>systemctl restart apache2</sub>

  <sub>systemctl status apache2</sub>

  <sub>systemctl restart postgresql</sub>

  <sub>nominatim serve</sub>


**#open browser and check the server running properly** 

run-: localhost/nominatim => OK

localhost/nominatim/reverse.php?format=json&lat=27.1750090510034&lon=78.04209025
