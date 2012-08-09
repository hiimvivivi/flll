# FetLife Export - Technical Documentation

The FetLife Export suite requires Perl (5.10.1 or later). If you want to install it on a website so others can use your copy, it also requires PHP (version 5.2 or later) and the Apache web server running on a UNIX-like operating system. For all features to work out-of-the-box, you’ll need to install the suite into and run it from its own domain or subdomain.

If you’re on a shared host, you may wish to compile your own Perl to do this, although in the vast majority of cases, you can probably [skip directly to installing requred CPAN modules](#installing-required-cpan-modules). Following are step-by-step instructions for installing from source.

## Installing from Source

Installing from source is the most reliable way to ensure everything is functional. It also may be required if your system lacks the necessary prerequisites for running `fetlife-export.pl`.

### Installing Perl

    wget http://www.cpan.org/src/5.0/perl-5.14.2.tar.gz
    tar -xvzf perl-5.14.2.tar.gz
    cd perl-5.14.2
    ./configure
    make
    make test # This is optional, but a good check. :)
    make install

### Installing required CPAN Modules

Most systems will have the modules you need. However, if you experience errors running fetlife-export.pl, you may also want to install all the required components yourself. To do this, run the following commands after you’ve installed your Perl:

    cpan App::cpanminus
    cpanm WWW::Mechanize
    cpanm HTML::TreeBuilder
    cpanm String::Escape
    cpanm Unicode::Escape

### Configure the tool

* Edit the shebang line in fetlife-export.pl to point to the perl you want to use.
* Make sure the `fetlife-export.pl` script is executable by running `chmod u+x fetlife-export.pl`.

At this point, you can use the tool from a command line by invoking it as follows:

    ./fetlife-export.pl username

Obviously, replace `username` with the username you’d like to export. You’ll be prompted for a password. If you supply a valid password, running the program will give you some output that looks something like the following example:

    $ ./fetlife-export.pl fetfails
    Password: 
    userID: 959391
    Loading profile: .
    Loading conversations: . 2 conversations found.
    Loading wall: . 0 wall-to-walls found.
    Loading activity feed: ... 9 statuses found.
    2 pictures found.
    1 writing found.
    0 group threads found.
    Downloading 9 statuses...
    Downloading 2 pictures...
    Downloading 1 posts...

The Web portion of the suite is mostly a simple wrapper around this command-line tool that provides an HTML interface to its options.

### Set options

Default options are provided in several files. To use them, simply run the following commands on your web server:

    cp htaccess-default .htaccess

## Troubleshooting

If you’re running PHP as a CGI or FastCGI, such as by using `mod_fcgi`, you may begin to frequently experience an “Internal Server Error” if a user with a large FetLife history attempts an export. This may appear in your Apache error log as “Premature end of script headers.” To resolve this, try setting the various timeouts, such as the [FcgidIOTimeout directive](https://httpd.apache.org/mod_fcgid/mod/mod_fcgid.html#fcgidiotimeout "Apache manual page for FcgidIOTimeout directive."), to high values in your server config:

    <IfModule mod_fcgid.c>
    # Set a high timeout for large FetLife export calls.
    # This may help avoid 500 Internal Server Errors.
    # 3600 seconds = 1 hour
    FcgidIdleTimeout 3600
    FcgidIOTimeout 3600
    </IfModule>

After you edit your server config file, remember to restart the web server, or at least reload the config file so the changes take effect:

    /etc/init.d/httpd2 reload

See also [Apache mod_fcgid read data timeout error](http://rickchristie.com/blog/2011/note/apache-mod_fcgid-read-data-timeout-error/).