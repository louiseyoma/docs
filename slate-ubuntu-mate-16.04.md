# Slate on Ubuntu 16.04

## Node.js Setup

Node.js is a necessary dependency for JavaScript execution. Install Node.js using:

    $ sudo apt-get install nodejs

## RVM Setup

It is recommended to use RVM (Ruby Version Manager) to install an isolated version of Ruby and necessary Gems to a user account.

This will allow easy recovery in case any part of the installation process goes awry. Wiping and reinstalling the system Ruby and Gems is possible, but more of a hassle. 

### GNUPG Key

First, we need to install the gpg key. 

As of this writing, the instructions on the [RVM Installation Documentation](https://rvm.io/rvm/install) fails:
    
    $ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
    ...
    gpg: requesting key D39DC0E3 from hkp server keys.gnupg.net
    gpg: requesting key 39499BDB from hkp server keys.gnupg.net
    ?: [fd 4]: read error: Connection reset by peer
    gpgkeys: HTTP fetch error 7: couldn't connect: eof
    ?: [fd 4]: read error: Connection reset by peer
    gpgkeys: HTTP fetch error 7: couldn't connect: eof
    gpg: no valid OpenPGP data found.
    gpg: Total number processed: 0
    gpg: keyserver communications error: keyserver unreachable
    gpg: keyserver communications error: public key not found
    gpg: keyserver receive failed: public key not found

Instead, go ahead and run:

    $ curl -sSL https://rvm.io/mpapis.asc | gpg2 --import -
    gpg: WARNING: unsafe ownership on configuration file `/home/mks/.gnupg/gpg.conf'
    gpg: key D39DC0E3: public key "Michal Papis (RVM signing) <mpapis@gmail.com>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)
    gpg: no ultimately trusted keys found

### Install RVM

Now we are okay to follow the remaining instructions on the [RVM Installation Documentation](https://rvm.io/rvm/install).

Install the stable version of RVM for a single user:

    $ curl -sSL https://get.rvm.io | bash -s stable

### Set Gnome Terminal as Startup Terminal

See [Integrating RVM with gnome-terminal](https://rvm.io/integration/gnome-terminal) for a more in-depth explanation. 

In a nutshell, RVM adds a script to `/etc/profile.d` to ensure that RVM is correctly loaded whenever a new shell is opened. However, in Ubuntu Mate, this file is not sourced by default when new terminals are opened up.

To fix this, it is necessary to do the following:

* Open up the Gnome Terminal (shortcut: Ctrl-Alt-T)
* Go to Edit > Profile Preferences.
* Under "Title and Command" > **Command**, select "Run command as a login shell"

### Verify RVM Loads Correctly

Check that `rvm is a function` like so:

    $ type rvm | head -n 1
    rvm is a function

If the return is anything other than `rvm is a function`, then RVM is prone to not functioning correctly.

Don't proceed until you are able to get `rvm is a function` back!

## Ruby Setup using RVM

Just run:

    $ rvm

For help on how to use it.

### Listing Ruby Versions

To list all versions of Ruby that can be installed by RVM, use:

    $ rvm list known
    # MRI Rubies
    [ruby-]1.8.6[-p420]
    [ruby-]1.8.7[-head] # security released on head
    [ruby-]1.9.1[-p431]
    [ruby-]1.9.2[-p330]
    [ruby-]1.9.3[-p551]
    [ruby-]2.0.0[-p648]
    [ruby-]2.1[.10]
    [ruby-]2.2[.7]
    [ruby-]2.3[.4]
    [ruby-]2.4[.1]
    ruby-head
    ...

As of this writing, it is recommended to install `ruby-2.3` for Slate.

### Install Ruby

First, ensure that `apt-get` or `synaptic` are not running, e.g. there is no lock on `/var/lib/dpkg`, then run:

    $ rvm install 2.3

This will possibly install additional system packages before installing the latest Ruby 2.3.x, hence why `synaptic`, `apt-get`, `dpkg`, etc. should not be running.

### Use Ruby

Check `which` version of Ruby is being used. Chances are, it is still the system Ruby:

    $ which ruby
    /usr/bin/ruby

To use the RVM Ruby 2.3, run any of the following commands:

    $ rvm 2.3
    $ rvm ruby-2.3
    $ rvm use 2.3
    $ rvm use ruby-2.3

## Gemset Setup using RVM

Gemsets are a way of organizing collections of Gems. By default, a `@global` Gemset is used, so for Ruby 2.3, the `ruby-2.3@global` Gemset is the default.

### Create a new Gemset

For added convenience, it is recommended to install Slate Gems to a separate (non-global) Gemset. To do so, first create a new Gemset using RVM:

    $ rvm gemset create slate
    ruby-2.3.x - #gemset created /home/<user>/.rvm/gems/ruby-2.3.x@slate
    ruby-2.3.x - #generating slate wrappers........

Then utilize the new Gemset:

    $ rvm use 2.3@slate
    Using /home/<user>/.rvm/gems/ruby-2.3.x with gemset slate

## Install Dependencies

First, make sure you are using the appropriate Gemset:

    $ rvm use 2.3@slate

Install `bundler`:

    $ gem install bundler

Navigate to the directory you cloned Slate into. Following the instructions in [the Github README](https://github.com/lord/slate), install gem dependencies using:

    $ bundle install

You may need to manually install additional system packages (development and header files) for compilation. Be sure to carefully read the error messages if you encounter a failure.

## Use Slate

Simply edit `source/index.html.md` in the Slate directory. While in the top-level of the Slate directory, publish your changes using:

    $ bundle exec middleman build --clean

Alternatively, simply run `deploy.sh`:

    $ ./deploy.sh

## Rendered Output

View `build/index.html`.

## (Optional) Run `middleman` Server

Slate uses the `middleman` server to offer a more convenient way to render and preview your changes without having to manually re-build. Instead, it is possible to make your changes to `index.md.html` and simply refresh the browser to view an updated render of your documentation. 

To start the `middleman` server, navigate to the Slate directory and run:

    $ bundle exec middleman server

This will start the server instance in the console window on port `4567`. Rendered Slate output can then be viewed at: `localhost:4567`.

Now, any time you make changes to `index.md.html`, simply refresh `localhost:4567` to view an updated render.
