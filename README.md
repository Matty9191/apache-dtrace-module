# Apache DTrace Module

I created this module to learn how Apache modules work (specifically the hook framework). This module is a POC and is not intended for production use (there are far better ways to visualize Apache specifics). Additional documentation is provided on my website:

http://prefetch.net/projects/apache_modtrace/index.html

# Build instructions:
<pre>
Step 1. Make sure Apache 2.X and Sun's C compiler are installed

Step 2: Download the mod_dtrace.c and apache.d files:
  $ wget http://prefetch.net/projects/apache_modtrace/mod_dtrace.c
  $ wget http://prefetch.net/projects/apache_modtrace/apache.d


Step 3: Create the following mapfile to scope functions local:
  $ cat mapfile
   data = R0x1000000;
   {
        local:
                apache_receive_request;
                apache_log_request;
                apache_create_child;
                apache_accept_connection;
                apache_check_user;
                apache_check_access;
                apache_check_authorization;
                dtrace_register_hooks;
   };


Step 4: Use the Sun cc compiler to build a relocatable object:
   $ cc -Mmapfile -I/usr/apache2/include -c -o mod_dtrace.o mod_dtrace.c


Step 5: Run dtrace on the relocatable object to add the pertinent ELF headers:
   $ /usr/sbin/dtrace -G -o apache.o -s apache.d mod_dtrace.o


Step 6: Create a shared object that can be used with Apache
   $ cc -Mmapfile -G -o mod_dtrace.so mod_dtrace.o apache.o

 
Step 7: Install the shared object in the /usr/apache2/libexec directory:
   $ cp mod_dtrace.so /usr/apache2/libexec


Step 8: Add a LoadModule directive to the httpd.conf configuration file:
   LoadModule dtrace_module libexec/mod_dtrace.so


Step 9: Verify that the probes are available by running the dtrace command:
   $ dtrace -l | grep apache


Step 10: Learn about your web server by writing some interesting D scripts.
</pre>
