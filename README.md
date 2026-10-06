# FreePBX Shared Phonebook

A shared PHP phonebook endpoint for:

- Digium/Asterisk A-series phones
- Alcatel M3s phones

The endpoint automatically returns the correct XML format based on the request.

## Requirements

- FreePBX/Debian-based PBX (17)
- Apache 2
- PHP
- HTTPS configured for Alcatel phones
- Root access
- `apache2-utils`, `curl`, `openssl`, and `python3`

## Installation

Download and run the installer as root:

```bash
sudo curl -fsSL \
  https://raw.githubusercontent.com/jeffreythebuilder/phonebook/main/configure-phonebook-auth \
  -o /usr/local/sbin/configure-phonebook-auth

Set ownership and permissions:
sudo chown root:root /usr/local/sbin/configure-phonebook-auth
sudo chmod 750 /usr/local/sbin/configure-phonebook-auth

Create the initial Apache configuration directory
sudo mkdir -p /etc/apache2/conf-available
sudo touch /etc/apache2/conf-available/phonebook-security.conf

This is required only for the first installation with the current installer.
Run the installer
sudo /usr/local/sbin/configure-phonebook-auth
The installer asks for:
1. Phone type:
- Alcatel only
- Digium only
- Both
2. Digium allowed IP networks
3. Phonebook username
4. Phonebook password
5. PBX hostname or IP address
Password Requirements
For Digium deployments:
- Minimum: 8 characters
- Maximum: 16 characters
- Use URL-safe characters only:
- Letters
- Numbers
- .
- _
- -
- ~
The phonebook username and password are separate from:
- SIP credentials
- FreePBX administrator credentials
- Digium phone web-management credentials
Digium Configuration
Digium/Asterisk A-series phones normally use HTTP on port 2001.
Example URL:
http://PBX_IP:2001/phonebook.php?format=digium
Configure the phone's XML phonebook authentication as:
XML-PBook1 Auth: username:password
The username must exactly match the Apache phonebook username.
Example:
XML-PBook1 Addr: http://172.16.138.13:2001/phonebook.php?format=digium
XML-PBook1 Auth: admin:Example1234
Digium access requires both:
- Valid HTTP Basic Authentication credentials
- A source IP inside the configured Digium network
Example allowed network:
192.168.188.0/24
Older Digium phones may not support HTTPS for remote phonebooks. HTTP should therefore be limited to a trusted voice VLAN or dedicated subnet.
Alcatel M3s Configuration
Use HTTPS with credentials embedded in the URL:
https://username:password@PBX_HOST/phonebook.php?format=alcatel
Example:
https://admin:Example1234@demo.example.com/phonebook.php?format=alcatel
The Alcatel M3s URL should remain within the tested approximately 127-character limit.
Security
The installer:
- Creates an Apache Basic Authentication password file
- Restricts Digium access by IP network
- Requires HTTPS for Alcatel access
- Uses a dedicated phonebook credential
- Backs up existing phonebook files and authentication files
- Validates the downloaded PHP file
- Validates the Apache configuration before reload
HTTP Digium credentials are not encrypted in transit. Use a trusted and restricted voice network.
Do not use:
- SIP passwords
- FreePBX administrator passwords
- Phone web-management passwords
Files
File	Purpose
/var/www/html/phonebook.php	Phonebook endpoint
/usr/local/sbin/configure-phonebook-auth	Installer
/etc/apache2/phonebook.htpasswd	Apache password file
/etc/apache2/conf-available/phonebook-security.conf	Apache access rules
/root/phonebook-backups/	Installer backups
Testing
Check Apache configuration:
sudo apache2ctl configtest
Test Digium access from an allowed network:
curl -u 'username:password' \
  'http://PBX_IP:2001/phonebook.php?format=digium'
Test Alcatel access:
curl -k \
  'https://username:password@PBX_HOST/phonebook.php?format=alcatel'
A successful response should return XML.
Updating
Download the installer and phonebook.php from a pinned Git commit rather than a mutable branch such as main.
Never commit:
- Passwords
- .htpasswd files
- Backup files
- Generated Apache configurations containing deployment-specific data
