---
title: 'Setting up a Mapnik Server on Ubuntu'
date: 2008-04-27T20:30:00.005+10:00
draft: false
url: /2008/04/setting-up-mapnik-server-on-ubuntu_118.html
tags: 
- howtos
---

First, we go ahead and install the needed packages. I've tried to include "my" list of packages that were needed to get a vanilla 7.10 image up to steam.

```
apt-get install build-essential libltdl3-dev autoconf libtool automake \\
postgresql postgresql-8.2-postgis postgresql-server-dev-8.2 \\
wget subversion libboost-python1.34.1 libboost-thread-dev \\
libboost-program-options-dev libboost-regex-dev \\
libboost-python-dev libboost-serialization-dev \\
libboost-filesystem-dev libpng12-dev libjpeg62-dev \\
libtiff4-dev zlib1g-dev libfreetype6-dev libgeos-dev \\
unzip apache2-prefork-dev

```  
  

Next we start to download a few components. I did this in my home directory, /home/kelvin

**mod\_tile** - this is the apache module and rendering daemon that uses mapnik to render the maps.

```
svn co http://svn.openstreetmap.org/applications/utils/mod\_tile

```  

**Mapnik** - this will help us create the maps.

```
wget http://download.berlios.de/mapnik/mapnik\_src-0.5.1.tar.gz

```  
  

Now we start to install things.

```
tar -xpzf mapnik\_src-0.5.1.tar.gz
cd mapnik-0.5.1

```  
  

Build mapnik as per: http://wiki.openstreetmap.org/index.php/Mapnik -- make sure to use scons as follows:

```
python scons/scons.py PYTHON=/usr/bin/python \\
PGSQL\_INCLUDES=/usr/include/postgresql \\
PGSQL\_LIBS=/usr/lib/postgresql BOOST\_INCLUDES=/usr/include/boost BOOST\_LIBS=/usr/lib

```  
  

Now, I'm temporarily serving/rendering my tiles from an old Thinkpad "server" (PIII with 512MB RAM, of which only 128MB goes to the Xen instance that hosts all of this). So, I am using osm2pgsql on my laptop (a new Thinkpad), and pushing it into the postgres database on my "server". So, I built osm2pgsql on my new Thinkpad, and setup postgres on the "server" to accept connections from my new Thinkpad.

**pg\_hba.conf** -- Set these lines should be added, assuming your computer is 192.168.10.100:

```
host    all     all     192.168.10.100/32  trust

```  

Then I do the actual import, assuming my "server" has an IP of 192.168.10.10:

```
./osm2pgsql -H 192.168.10.10 -U username -l -m -d gis -W /home/location/to/osm/australia.osm

```  
  

Make sure generate\_image works before installing mod\_tile!

Install mod\_tile as per the modifications needed: http://www.kelvinism.com/howtos/notes-installing-mod\_tile-mapnik/

And the end result? [http://tiles.kelvinism.com](http://tiles.kelvinism.com) (remember, on a seven year old Thinkpad!)