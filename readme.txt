installing ejabberd from source
http://wiki.laptop.org/go/Installing_ejabberd

########################

1. Don’t have ejabberd installed from the Ubuntu archives - we will build it from source. You will have to enable the "universe" package repositories.
2. Install dependencies:
sudo apt-get install erlang-base erlang-nox erlang-dev build-essential libssl-dev libexpat-dev
3. Get the ejabberd source:
mkdir ejabberd
cd ejabberd
wget http://www.process-one.net/downloads/ejabberd/2.0.0/ejabberd-2.0.0.tar.gz
tar xfz ejabberd-2.0.0.tar.gz
4. Get and apply the OLPC-specific patches and configuration:
wget http://people.collabora.co.uk/~robot101/olpc-ejabberd/shared_roster_push2.diff
wget http://people.collabora.co.uk/~robot101/olpc-ejabberd/shared_roster_recent.diff
wget http://people.collabora.co.uk/~robot101/olpc-ejabberd/shared_roster_online.diff
wget http://people.collabora.co.uk/~robot101/olpc-ejabberd/mod_ctlextra.diff
wget http://people.collabora.co.uk/~robot101/olpc-ejabberd/ejabberd.cfg
cd ejabberd-2.0.0/
patch -p1 < ../shared_roster_push2.diff
patch -p1 < ../shared_roster_recent.diff
patch -p1 < ../shared_roster_online.diff
patch -p1 < ../mod_ctlextra.diff
5. Build ejabberd:
cd src
./configure
make
(Although I saw “warning: pointer targets in assignment differ in signedness” at this point, the build did succeed.)
6. Install:
sudo make install
7. Apply configuration:
cd ../..
sudo cp ejabberd.cfg /etc/ejabberd/
sudo gedit /etc/ejabberd/ejabberd.cfg
(or use your preferred editor.)
Change the hostname: find the line that says
{hosts, ["jabber.laptop.org"]}.
and set it to the FQDN of your server:
{hosts, ["your.host.name"]}.
Do the same for the admin login: find the line that says
{acl, admin, {user, "jtest", "jabber.laptop.org"}}.
and set the FQDN to your server:
{acl, admin, {user, "jtest", "your.host.name"}}.
(The username "jtest" is for admin rights accessing the web interface. You can change it to something else, but keep it consistent.)
8. Create an SSL key and certificate:
openssl req -newkey rsa:1024 -keyout ejabberd.pem -nodes -x509 -days 3650 -out ejabberd.cer
echo "" >> ejabberd.pem
cat ejabberd.cer >> ejabberd.pem
sudo cp ejabberd.pem /etc/ejabberd/ejabberd.pem
sudo chown ejabberd:ejabberd /etc/ejabberd/ejabberd.pem
sudo chmod 400 /etc/ejabberd/ejabberd.pem
9. Start the server:
sudo ejabberdctl start
You should be able to use sudo /etc/init.d/ejabberd start, but that didn’t work for me (yet). This will do it, but won’t start automatically on boot.--morgs 08:49, 4 March 2008 (EST)
Required using sudo ejabberdctl ejabberd@servername start for me (/user/sbin/) ridd 21 May 08
Add username@host to ejabberdctl.cfg to use sudo ejabberdctl by itself -- nubae Oct 16 08

