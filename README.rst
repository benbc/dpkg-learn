============
 dpkg-learn
============

I created this tool to help me understand how to write Debian package
maintainer scripts. It creates an empty package and carries out
various `dpkg` operations on it. For each operation, it traces out
which maintainer scripts get executed with which arguments.

To run the tool execute ``rake``.

**Warning** Running this tool will install a package on your system.
The package is empty and the tool removes it again, but if something
 goes wrong then you may need to tidy up.

The tool requires Ruby and Rake (and a Debian-based system,
obviously). It has been tested only on Ubuntu 10.10 with Ruby 1.8.7.
