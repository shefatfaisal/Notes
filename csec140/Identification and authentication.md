5 topics covered
- Identification
	- Identifiers
	- groups
- authentication
- Multi-factor authentication
- single sing-on
- Mutual Authentication
**Computer IDs**
- usernames
- special users
	- root
		- System administrator
	- Daemon
		- Handle operations ex: network, cron jobs
	- Nobody
		- owns no file
		- Default users for unprivileged users
		- Web server runs with this mode

Groups
- **wheel group**: is a special user group they can control access to the su or sudo command
- stored in **/etc/group**

**Authentication** - Proves who you are


Password Attack

- Guessing attack or Dictionary attack

Mutual Authentication (Avoids Man-in-the middle attack)
