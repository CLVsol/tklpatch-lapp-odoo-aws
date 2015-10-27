tklpatch-lapp-odoo-aws
======================

This project will help you install `Odoo 8.0 <https://www.odoo.com/>`_ over a `TurnKey LAPP <http://www.turnkeylinux.org/lapp>`_ appliance, using the Amazon Web Services (AWS) EC2 infrastructure.

#. Create a new Key Pair:

	* Key pair name: **tkl-lapp-odoo-aws**
	* Private Key File: **tkl-lapp-odoo-aws.pem**

#. Create, via TrunKey Hub, an Amazon EC2 instance:

		- TurnKey Appliance: **LAPP Stack (14.0)**
		- Hostname: **[tkl-lapp-odoo-aws]**
		- Region: **[Sao Paulo (South America)]**
		- Size: **[Micro ($0.027/hour)]**
		- SSH key-pair: **[tkl-lapp-odoo-aws]**
		- Root file system size (GB): **[10]**

	Related information:

		- Security Groups: **[turnkey-lapp-xxxx]**
		- Private Key File: **[tkl-lapp-odoo-aws.pem]**

	Security Group: [turnkey-lapp-xxxx] (Inbound)::

		Port (Service)   Source
		-------------------------------------
		N/A(PING)        0.0.0.0/0
		22(SSH)          0.0.0.0/0
		80(HTTP)         0.0.0.0/0
		443(HTTPS)       0.0.0.0/0
		12320(Web Shell) 0.0.0.0/0  (disable)
		12321(Webmin)    0.0.0.0/0  (disable)
		12322(Adminer)   0.0.0.0/0  (disable)

#. Upgrade the software:

	::

		apt-get update
		apt-get -y upgrade

#. Install **TKLPatch** using the commands:

	::

		apt-get update
		apt-get -y install tklpatch

	The documentation is installed at "/usr/share/tklpatch/docs" and the exemples at "/usr/share/tklpatch/docs".

#. Get the TKLPatch script "**clvsol_tklpatch-lapp-odoo-aws**" using the commands:

	::

		cd /root
		git-clone https://github.com/CLVsol/tklpatch-lapp-odoo-aws.git clvsol_tklpatch-lapp-odoo-aws

#. Apply the patch "clvsol_tklpatch-lapp-odoo-aws":

	::

		cd /root
		tklpatch-apply / clvsol_tklpatch-lapp-odoo-aws

#. `Disable Password-based Login <http://aws.amazon.com/articles/1233?_encoding=UTF8&jiveRedirect=1>`_:

	Log in to your instance as root and edit the ssh daemon configuration file "**/etc/ssh/sshd_config**"

	Find the line::

		PasswordAuthentication yes

	and change it to::

		PasswordAuthentication no

	Save the file and restart sshd::

		/etc/init.d/ssh restart

	**You will now only be able to log in with an ssh key.**

#. Update host name, executing the following commands:

	::

		HOSTNAME=tkl-lapp-odoo-aws
		echo "$HOSTNAME" > /etc/hostname
		sed -i "s|127.0.1.1 \(.*\)|127.0.1.1 $HOSTNAME|" /etc/hosts
		/etc/init.d/hostname.sh start

#. Copy file "/etc/odoo/openerp-server.conf" into "/etc/odoo/openerp-server-man.conf". Edit the file "/etc/odoo/openerp-server-man.conf":

	::

			#logfile = False
			logfile = /var/log/odoo/openerp-server.log

	::

			logfile = False
			#logfile = /var/log/odoo/openerp-server.log


#. (Optional) Reboot the instance "tkl-lapp-odoo-aws".

#. To stop and start the Odoo server, use the following commands (as root):

	::

		/etc/init.d/openerp-server stop

		/etc/init.d/openerp-server start

	::

		cd /opt/openerp/odoo
		su openerp
		./openerp-server -c /etc/odoo/openerp-server-man.conf
