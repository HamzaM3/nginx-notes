There are 5 kinds of directives:for 

- Core directives
- http directives
- mail directives
- stream directives
- Google perfomance tools module

Also there are some variables that can be used in the directives.

Quick reminder:

Config file is a sequence of directives
A directive is either simple or a block
A simple directive is a name + parameters separated by spaces:
- ex: user www-data;
A block directive is a name + parameters + a curly braces block of directives (called context)
- location /images/ { root /data/images/; }

Variables are used in parameters

Main context = outside of any block directive's context