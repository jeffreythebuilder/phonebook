# FreePBX Shared Phonebook

Simple shared phonebook for **FreePBX 17**.

Supports:

- Digium / Asterisk A-Series phones
- Alcatel M3s phones
- FreePBX Contact Manager

The installer automatically configures the phonebook, authentication and Apache access.

## Requirements

- FreePBX 17 / Debian
- Root access
- A Contact Manager group with contacts
- HTTPS configured if using Alcatel M3s

## Installation

Run:

```bash
sudo curl -fsSL \
https://raw.githubusercontent.com/jeffreythebuilder/phonebook/main/configure-phonebook-auth \
-o /usr/local/sbin/configure-phonebook-auth

sudo chmod 750 /usr/local/sbin/configure-phonebook-auth

sudo /usr/local/sbin/configure-phonebook-auth
```

Follow the on-screen questions.

The installer will ask you to select:

- Phone type: Alcatel, Digium or both
- FreePBX Contact Manager group
- Digium phone network, if required
- Phonebook username and password
- PBX hostname or IP address

When installation is complete, the correct phonebook URLs will be displayed.

## Digium / Asterisk A-Series

Example:

```text
http://PBX_IP:2001/phonebook.php?format=digium
```

Configure the phone with:

```text
XML-PBook1 Addr: http://PBX_IP:2001/phonebook.php?format=digium
XML-PBook1 Auth: username:password
```

Digium access requires:

- Correct username/password
- Phone IP must be inside the allowed network

## Alcatel M3s

Example:

```text
https://username:password@PBX_HOST/phonebook.php?format=alcatel
```

HTTPS is required for Alcatel access.

## Security

Use a dedicated username and password for the phonebook.

Do **not** use your:

- SIP password
- FreePBX administrator password
- Phone web-management password

Digium uses HTTP on port `2001`, so it should only be used on a trusted voice VLAN or internal network.

## Reconfigure

To change the settings later, simply run the installer again:

```bash
sudo /usr/local/sbin/configure-phonebook-auth
```

Existing files are backed up automatically.
