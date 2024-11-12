# Installation Guide

Follow this guide to set up an edge server and one or more Microsoft HoloLens 2
headsets.

## Requirements

For the edge server, it is recommended to use an Ubuntu server of
any recent LTS release that supports snapd. Both x86_64 and ARM64
architectures are supported.  For lowest latency, it is recommended that
the edge server be situated as close on the network as possible to the
headsets. The edge server could be colocated with the WiFi access point
that serves the headsets, or it could be in a geographically proximal
data center.

* Ubuntu 18.04, 20.04, 22.04, x86_64 or ARM64
* Minimum 4 GiB RAM and 2 CPU cores
* Minimum 8 GiB free storage space, more if enabling image processing
* Recommended GPU with CUDA support if enabling image processing

For the AR headsets, we currently only support Microsoft HoloLens 2.
The HoloLens devices need to have the Windows Device Portal enabled
and Research Mode enabled.

* [Enable Windows Device Portal](https://learn.microsoft.com/en-us/windows/mixed-reality/develop/advanced-concepts/using-the-windows-device-portal)
* [Enable Research Mode](https://learn.microsoft.com/en-us/windows/mixed-reality/develop/advanced-concepts/research-mode)

# Edge Server Setup

The recommended installation assumes an Ubuntu LTS server and relies on snaps
for distribution and automatic updates. Snaps contain most of the application
dependencies, so these instructions should work on various Ubuntu versions
including 18.04 through 22.04.

Install the EasyVizAR edge server. By default, this will pull from the stable
branch, which will only receive infrequent and tested releases.

```console
sudo snap install easyvizar-edge
```

The server will listen on port 5000 and can be tested by directing a web
browser to the server address and port, e.g. `http://example.org:5000`.
However, in a production setting, it is recommended to block external traffic
to port 5000 and instead use a proxy, such as nginx as described below.

Install nginx and the rtmp module.

```console
sudo apt-get update && sudo apt-get install nginx libnginx-mod-rtmp
```

(Optional but recommended) Install the Let's Encrypt certbot for free SSL
certificates, in order to enable HTTPS connections.

```console
sudo snap install certbot --classic
```

Request a certificate for the server's domain name.

```console
sudo certbot run --nginx -d EXAMPLE.ORG
```

Then customize the nginx configuration and restart the nginx server. A couple
of recommended options are enabling the rtmp module for real-time video
streaming and enabling automatic redirects from HTTP to HTTPS. Example
configuration files are provided in the `environment` folder.

```console
sudo systemctl restart nginx
```

The server should now be available and can be tested by directing a web
browser to the intended domain name, e.g. `https://example.org`.

(Optional) Install the easyvizar-detect add-on module. This module
automatically performs object detection on any photos uploaded to the edge
server. It is currently experimental and may cause performance issues.

```console
sudo snap install easyvizar-detect
```

# Post-Installation Setup

## Users and Authentication

When the server starts for the first time, it will create three default user
accounts: admin, user, and guest. The admin and user accounts will be
configured with randomly generated passwords that can be found in the log
output.  The guest account will have no password configured by default.  If
installed as a snap, the following command will reveal the default account
passwords. You may need to open a connection to the server through a web
browser one time in order for the initialization to complete.

```console
sudo snap logs -n=all easyvizar-edge | grep user
```

It is recommended to change the admin and user account passwords after
installation. This can be done on the Users page of the web dashboard if
logged in as an admin user. The default admin and user accounts should not
be deleted.  However, the guest account can be safely deleted if desired.

## Create a Location

In the EasyVizAR system, a *location* is any defined physical space to
which virtual content can be anchored. A location might be a multi-floor
office building, for example. The server will generate a unique QR code
for each location. By scanning the QR code, EasyVizAR-capable devices can
report their presence and synchronize their virtual coordinate system,
with the QR code serving as the origin of the coordinate system.

Navigate to the Locations page on the web dashboard and use the form to
create a new location. Then click on the newly-created location ID link in
the table to go to page for that location. Initially, the map and tables
will be empty but will be populated as devices join the location and send
data. Click on the *Location QR Code* button to generate a QR code. It
is recommended to print the QR code on letter size paper and afix it
**horizontally** to a flat surface in the environment, roughly between
waist and eye-level. Taping it to a table works well.  The coordinate
system for the location will have its origin at the center of the QR
code, X-axis pointing to the right edge of the page, Y-axis pointing up
from the QR code, and Z-axis pointing toward the top edge of the page.
Floor plan maps will be generated in the same orientation as the QR code
(imagine the QR code block were replaced with a map), so you may wish to
orient the page according to how you would like the map to be generated.

# HoloLens 2 Setup

The currently recommended installation process is to download a release build
of our HoloLens application and install through the Windows Device Portal.

Connect HoloLens to a trusted WiFi network and find the IP address of the
headset. You can use the voice command "what is my IP" or open the system
settings menu.

Load the device portal webpage using the IP address of the headset. The browser
may warn about the security of the connection. That is expected and OK.

Navigate to Views, then Apps. Under the Deploy Apps section, select Network
and enter the URL for the latest release of our HoloLens app.

    https://github.com/widVE/NIST/releases/download/v1.1.0/EasyVizAR_1.1.0.0_arm64_Master.msixbundle

From the HoloLens application menu, launch the EasyVizAR app. When prompted,
grant permission for camera and microphone usage.

Then, while running the EasyVizAR app, if you scan a location QR code, it
will automatically begin tracking and data collection.

# Tutorial Videos

Once the server software is installed, watch the command tutorial video
video [here](https://youtu.be/HPPaKLYRelc) to learn how to use the most
commonly-used features.

Likewise, after loading the application on a HoloLens 2 headset, watch the headset
tutorial video [here](https://youtu.be/6akYBMIhIvE), which covers the features
of the headset application.
