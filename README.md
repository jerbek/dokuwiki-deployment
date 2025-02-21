<p align="center">
<img src="https://i.imgur.com/N9dgpOB.png" alt="dokuwiki logo"/>
</p>

<h1>DokuWiki - Prerequisites and Installation</h1>
This guide outlines the prerequisites and installation of the open-source knowledge base software DokuWiki.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Tailscale (VPN stack)
- SSH
- DNS

<h2>Operating Systems Used </h2>

- Ubuntu Linux

<h2>List of Prerequisites</h2>

- apache2
- libapache2-mod-php
- php
- php-fpm
- php-json
- php-gd
- php-intl
- php-mbstring
- php-zip
- php-xml

<h2>Installation Steps</h2>

<p>
  Deploy an Ubuntu VM with your SSH keys and install Tailscale (register and install this on your PC if you haven't already)
</p>
<p>
<img src="https://github.com/user-attachments/assets/5829c5c1-aa83-48e8-837e-fd86d528b068" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
  Make an A record in your Windows DNS server pointing towards the Ubuntu VM's internal Tailscale IP then add an entry for Magic DNS in Tailscale's admin console
</p>
<p>
 Disable the open SSH port in your Ubuntu VM's Azure network settings and connect via the Tailscale IP</p>
<p>
<p>
<img src="https://github.com/user-attachments/assets/0fbea5ea-74ac-43df-ae17-ff82ec3f4c95" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
  Install all the prereqs with <code>sudo apt update && sudo apt install -y apache2 libapache2-mod-php php php-fpm php-json php-gd php-intl php-mbstring php-zip php-xml php-ldap</code>
<p>
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
  Choose what DokuWiki download you want from the official website and wget get the download URL on your VM
<p>
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
  Extract your DokuWiki archive to /var/www/html and change the ownership so the web server might use it with <code>sudo chown www-data:www-data -R /var/www/html</code>
</p>
<p>
  For basic security, change the permissions of your web directory with <code>sudo chmod 770 -R /var/www/html</code> and <code>sudo chmod 775 -R /var/www/html/data/cache</code>
</p>
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
  Open your website via your internal domain and finish the web installation
</p>
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
  Configure the apache2 site for rewrite and denying access to sensitive directories by putting the following inside the VirtualHost block of /etc/apache2/sites-available/000-default.conf
  <pre>
<code>
    <Directory />
        Options FollowSymLinks
        AllowOverride None
    </Directory>

    <Directory /var/www/html/>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride None
        Require all granted

	# Redirect to clean URLs
 	<IfModule mod_rewrite.c>
		RewriteEngine on
		RewriteBase /

		RewriteRule ^_media/(.*)              lib/exe/fetch.php?media=$1  [QSA,L]
		RewriteRule ^_detail/(.*)             lib/exe/detail.php?media=$1 [QSA,L]
		RewriteRule ^_export/([^/]+)/(.*)     doku.php?do=export_$1&id=$2 [QSA,L]
		RewriteRule ^$                       doku.php  [L]
		RewriteCond %{REQUEST_FILENAME}       !-f
		RewriteCond %{REQUEST_FILENAME}       !-d
		RewriteRule (.*)                      doku.php?id=$1  [QSA,L]
	</IfModule>
   </Directory>


    # Block access to sensitive folders
    <Directory /var/www/html/data>
	Require local
	Deny from all
	Allow from 127.0.0.1
	Satisfy All
    </Directory>
    <Directory /var/www/html/conf>
	Require local
	Deny from all
	Allow from 127.0.0.1
	Satisfy All
    </Directory>
    <Directory /var/www/html/bin>
	Require local
	Deny from all
	Allow from 127.0.0.1
	Satisfy All
    </Directory>
    <Directory /var/www/html/inc>
	Require local
	Deny from all
	Allow from 127.0.0.1
	Satisfy All
    </Directory>
    <Directory /var/www/html/vendor>
	Require local
	Deny from all
	Allow from 127.0.0.1
	Satisfy All
    </Directory>
</code>
</pre>
</p>
</p>
<br />

<p>
  Set the "Nice URLS" setting in the admin config to .htaccess. Then configure to taste.
</p>
<img src="https://github.com/user-attachments/assets/01408d6a-8063-4a68-af90-dce18529ed86" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
