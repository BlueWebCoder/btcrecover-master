# *seedrecover.py* #

*seedrecover.py* is a Bitcoin and Ethereum seed recovery tool which can take a seed with one or more mistakes in it, and recover the correct seed if not too many mistakes have been made.


## Installation ##

Just download the latest version from <https://github.com/gurnec/btcrecover/archive/master.zip> and unzip it to a location of your choice.

Additional requirements are described below.

### Windows ###

 1. Follow [these instructions](INSTALL.md#python-27) to download and install Python 2.7.

 2. Open a command prompt window, and type this to install the required Python libraries:

        C:\Python27\Scripts\pip install coincurve==5.2.0 pysha3

### Linux ###

Most distributions include Python 2.7 pre-installed. Two additional Python libraries, coincurve and (for Ethereum wallets) pysha3 are required. For example on Debian-like distributions (including Ubuntu), open a terminal window and type this:

    sudo apt-get install python-pip
    sudo pip install coincurve==5.2.0 pysha3

### OS X ###

 1. Follow [these instructions](INSTALL.md#os-x) to download and install the latest version of Python 2.7.

 2. Open a terminal window (open the Launchpad and search for "terminal"). Type this and then choose `Install` to install the command line developer tools:

        xcode-select --install

 3. Type this to install the GNU Multiple Precision Arithmetic Library: 
 
        curl -O https://gmplib.org/download/gmp/gmp-6.1.2.tar.xz
        tar xf gmp-6.1.2.tar.xz
        cd gmp-6.1.2
        ./configure --prefix=/usr/local/opt/gmp
        make && make check
        sudo make install
        cd ..
        rm -rf gmp-6.1.2 gmp-6.1.2.tar.xz

 4. Type this to install coincurve and (for Ethereum wallets) pysha3:

        sudo pip install coincurve==5.2.0 pysha3


## Running *seedrecover.py* ##

In order to run *seedrecover.py*, you'll need these two things:

 * A good estimate of what your seed is, *and*

 * *One* of these four, in order of preference:
     1. for Electrum (1.x or 2.x), a copy of your wallet file (a wallet file using Electrum 2.8's new full-file encryption won't work here), *or*
     2. your master public key (sometimes called an *xpub*), *or*
     3. a receiving address that was generated by your wallet from your seed, along with a good estimate of how many addresses you created before the receiving address you'd like to use, *or*
     4. an "address database". If you don't have i., ii., or iii. from above, please see the [Recovery with an Address Database](#recovery-with-an-address-database) section below.

To start *seedrecover.py* on OS X, first rename the `seedrecover.py` script file to `seedrecover.command`. Aside from this, starting it is the same for every system: just double-click the `seedrecover.py` (or `seedrecover.command`) file. If you're asked about running it in a terminal, choose *Run in Terminal*. Next, you'll be asked a short series of questions:

 1. First you'll be asked to open your wallet file. If you have an Electrum wallet file, find it now - the rest of the steps will then be skipped. Otherwise, click `Cancel` to continue.

 2. Next, select your wallet type. If you're unsure of what to choose, feel free to open an [issue on GitHub](https://github.com/gurnec/btcrecover/issues/new) to see if your wallet software is supported. 

 3. Next you'll be asked for your master public key. If you don't have yours stored anywhere, click `Cancel` to continue.

 4. If you don't have your master public key, next you'll be asked for your Bitcoin addresses. Find as many of your addresses associated with this wallet as you can, and enter them here (separated by spaces). Addresses you created early in your wallet's lifetime are prefereable. If your wallet supports multiple "accounts" each with their own address list, only addresses from your first account should be entered here.

 5. If you entered Bitcoin addresses above, next you'll be asked to enter the "address generation limit". *seedrecover.py* works by generating one or more addresses based on each seed it tries. The generation limit is the number of addresses it generates for each seed. Generating fewer addresses will improve *seedrecover.py*'s speed, however if it generates too few, it will miss the correct seed entirely.
 
    For example, let's say you found and entered three Bitcoin addresses in step 4. If you're reasonably sure that all three were within the first 10 addresses ever created in your wallet, you should use `10` for the address generation limit.

Finally, you'll be asked for your best guess of what your seed is.

### Recovery with an Address Database ###

When *seedrecover.py* tries different guesses based on the seed you entered, it needs a way to determine which seed guess is correct. Normally it uses each seed guess to create a master public key (an *mpk*) and compare it to the mpk you entered, or to create Bitcoin addresses and compare them to the addresses you entered. If you have neither your mpk nor any of your addresses, it's still possible to use *seedrecover.py* but it is more complicated and time consuming.

This works by generating Bitcoin addresses, just as above, and then looking for each generated address in the entire Bitcoin blockchain. In order to do this, you must first create a database of addresses based on the full blockchain:

 1. You must use a computer with at least 150GB of free drive space to store the full blockchain and at least 6 GB of RAM. You must have the 64-bit version of Python installed.

 2. Install a full-node Bitcoin client, such as [Bitcoin Unlimited](https://www.bitcoinunlimited.info/), [Bitcoin Classic](https://bitcoinclassic.com/), [Bitcoin XT](https://bitcoinxt.software/), or [Bitcoin Core](https://bitcoincore.org/).

 3. Start your Bitcoin client and allow it to fully sync. Depending on your Internet connection and your computer, fully syncing a node can take one or more days. Starting `bitcoin-qt` (or `bitcoind`) with the `-dbcache #` option can help: the `#` is the amount of RAM, in MB, to use for the database cache. If your computer has at least 8 GB of RAM, giving up to `4000` MB to the `-dbcache` will speed things up. Installing Bitcoin on a computer with an SSD can also help.

 4. Once your Bitcoin client is synced, close the Bitcoin software.

 5. (On OS X, rename the `create-address-db.py` script file to `create-address-db.command`.) Double-click on the `create-address-db.py` script (in the same folder as `seedrecover.py`) to build the address database using the fully-synced blockchain (it will be saved into the same directory as `create-address-db.py` with the name `addresses.db`) . This process will take about one hour, and use about 4 GB of both RAM and drive space.

 6. Follow the steps listed in the [Running *seedrecover.py*](#running-seedrecoverpy) section, except that when you get to the address entry window in step 4, click `Cancel`.

 7. For the next step, you still need to choose an address generation limit. This should be the number of unused addresses you suspect you have at the beginning of your wallet before the first one you ever used. If you're sure you used the very first address in your wallet, you can use `1` here, but if you're not so sure, you should choose a higher estimate (although it may take longer for *seedrecover.py* to run).

Note that *seedrecover.py* will use about 4 GB of RAM while it is running with an address database.