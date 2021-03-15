# ![](https://upload.wikimedia.org/wikipedia/en/thumb/4/45/MongoDB-Logo.svg/2880px-MongoDB-Logo.svg.png) Mongo DB Installfest

We will be installing [Mongo DB](https://www.mongodb.com/) - a popular open source **NoSQL** database. 

Mongo uses a 'document-oriented database' structure with very JSON-like documents and familiar javascript-like commands for database operations.

This installfest is adapted from Mongo DB's install instructions that can be found [here](https://docs.mongodb.com/manual/administration/install-community/).

## Linux

[Here](https://docs.mongodb.com/manual/administration/install-on-linux/) are instructions for installing on linux. Select the version of linux you have and follow those instructions.

## Windows

[Here](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/) Here are the windows install instructions. Follow the 'Service Configuration' installation instructions.

## Mac OS

We will be using `homebrew`, everyone's favorite package manager, for this install.

### Getting set up for install

Before we be begin, lets make sure we have the latest and greatest packages by checking to see if there are any updates for things we already have installed.

Run the following command to update you packages:

```bash
# upgrades all packages managed by homebrew
brew upgrade
```

This may take a moment while homebrew updates.

___

You should already have the xcode command line tools installed from the unit 1 install fest, but just to be sure run the following command:

```bash
xcode-select --install
```

If you get an error, that is a good thing! It means you already have the needed tools installed. Otherwise, it can take up to 5 min or so to download the package and install it, so be patient. 

___

### Installing MongoDB

Now lets install Mongo DB! Run the following two commands one after the other:

```bash
brew tap mongodb/brew

brew install mongodb-community@4.4
```

You, my friend, have just installed MongoDB! You can run `brew cleanup` to remove any tempory files used for the installation if you wish

___

Now that mongoDB is installed, you will be able to use the following commands to start/stop/restart the mongodb servies with the follwoing commands:

```bash
# start service
brew services start mongodb-community

# stop service
brew services stop mongodb-community

# restart service
brew services restart mongodb-community
```

You will need to have the mongodb service running in order to use the database -- so make sure it is started before you begin using mongodb!

___

### Using the MongoDB Shell

To the mongdb shell simply type the `mongo` into your terminal. You should see a shell like this:

![mongo db shell](/mongo-shell.png)

Congrats! MongoDB is successfully installed! You can exit the shell by typing `quit()`

The mongo shell is like the `psql` but for mongoDB instead of postgres.

___

### A little config

If you see an error that looks like this when the shell starts:

![shell-error](/error.png)

It means mongoDB is complaining that the memory allocation is too low for it run fast. Not a huge deal for developement machines, but still, we can fix that! 

You will have to create a launch deamon that raises the soft rlimit when your machine boots up. [Learn more about macOS launch deamons](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLaunchdJobs.html)

We need to create a launch deamon, give the current user permission to edit it, and then add some xml to run when your computer boots.

Run these commands one by one:

```bash
# create launch deamon
sudo touch /Library/LaunchDaemons/limit.maxfiles.plist

# let current user edit it
sudo chown $USER /Library/LaunchDaemons/limit.maxfiles.plist

# open it up in code
sudo code /Library/LaunchDaemons/limit.maxfiles.plist
```

Becasue we used the `sudo` command, you will be prompted to enter your password.

Copy the following `xml` to raise the soft rlimit when your computer boots:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
        "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>limit.maxfiles</string>
    <key>ProgramArguments</key>
    <array>
      <string>launchctl</string>
      <string>limit</string>
      <string>maxfiles</string>
      <string>262144</string>
      <string>524288</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>ServiceIPC</key>
    <false/>
  </dict>
</plist>
```

Save the document and the next time you reboot your machine, the launch deamon will work its magic!

