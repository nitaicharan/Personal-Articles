# Introducing Socket Wrench

Created: May 7, 2022 10:46 AM
Finished: No
Source: https://asleepysamurai.com/articles/socketwrench
Tags: #tool

Socket Wrench is to Websockets what Postman/Insomnia is to HTTP connections (kinda).

![Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/logo.png](Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/logo.png)

Socket Wrench is a super simple, bare-bones, downloadable, cross platform, desktop GUI application (yes, it's Electron), which makes testing websockets easy. I wrote this to scratch a personal itch, and after having friends and colleagues find it useful, decided to clean it up a bit, make it more user friendly, and released for public use.

### Features

- Multiple connections multiplexed into a single view. Makes it easier to see how one client's messages impact other clients.
    
    ![Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/multiplex.png](Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/multiplex.png)
    
- Complete message history. Makes it easy to review past sessions and re-use previous messages.
    
    ![Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/history.png](Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/history.png)
    
- Independent view, type and sending formats. Allows you to type yaml, send as xml and view it as json.
    
    ![Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/formats.png](Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/formats.png)
    
- Custom headers for initial HTTP connection.
    
    ![Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/headers.png](Introducing%20Socket%20Wrench%209f0c9250e4fd449385fd7d5c297fb08c/headers.png)
    
- Cross platform, offline, desktop application available for Mac OS X, Linux and Windows.

### Interested? Try it out now!

Socket Wrench is currently available in the following distributions:

- [Mac OS X](https://asleepysamurai.com/articles/socketwrench/binary/SocketWrench-0.0.1-setup.dmg) - DMG file. Mount it, copy Socket Wrench to your 'Applications' directory, and it's installed.
- [Windows](https://asleepysamurai.com/articles/socketwrench/binary/SocketWrench-0.0.1-setup.exe) - Installer Executable. Run the installer, and it'll auto install and launch Socket Wrench once it's done.
- Linux
    - [DEB](https://asleepysamurai.com/articles/socketwrench/binary/SocketWrench-0.0.1-setup.deb) - Debian Installer Package. Run the installer and it'll open up Software Center (or equivalent for your platform). Follow on-screen instructions to install.
    - [RPM](https://asleepysamurai.com/articles/socketwrench/binary/SocketWrench-0.0.1-setup.rpm) - RPM Installer Package. Run the installer and follow on-screen instructions to install.

### Issues, Bugs and Discussions

If you happen to find any bugs, or have ideas for a cool feature you want to see in Socket Wrench, hit up the [Github issues page](https://github.com/asleepysamurai/socketwrench/issues).