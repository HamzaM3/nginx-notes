There are 5 kinds of directives:for 

- Core directives
- http directives
- mail directives
- stream directives
- Google perfomance tools module

Also there are some variables that can be used in the directives.

Quick reminder:
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
#
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}

Config file is a sequence of directives
A directive is either simple or a block
A simple directive is a name + parameters separated by spaces:
- ex: user www-data;
A block directive is a name + parameters + a curly braces block of directives (called context)
- location /images/ { root /data/images/; }

Variables are used in parameters

Main context = outside of any block directive's context