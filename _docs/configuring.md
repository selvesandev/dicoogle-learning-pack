---
title: Configuring
permalink: /docs/configuring/
---

Dicoogle often needs to be configured manually to fulfill certain needs. There are also a few features that may not work as intended without proper configuration. This page will skim through the configurable parts of Dicoogle.

### Configuring the Indexers

Start by navigating to the Dicoogle web app and entering the **Management** menu. The first section of this menu is for indexer settings. It is also possible to enable the Dicoogle Directory Watcher, which creates a daemon that listens for new files in a directory and indexes them automatically. This is often useful when dragging files to the folder manually, but not when using Dicoogle as a DICOM server.

![]({{ site.baseurl }}/images/screenshot_config_index.png)

After selecting the configurations, the "Apply Settings" button must be pressed.

### Transfer Options

As Dicoogle is a working DICOM server, it can also be configured to implement certain Service-Object Pair (SOP) classes. DICOM transfer options can be defined here by checking or unchecking transfer syntaxes from the list. The _"Select All"_ button will enable all transfer syntaxes for all SOP classes. Pressing it again will perform the opposite.

![]({{ site.baseurl }}/images/screenshot_config_transfer.png)

### Configuring Services

In the Management menu, Services and Plugins tab, it is possible to start and/or stop currently running services in real time. Moreover, some configurations like the DICOM service ports may be redefined.

![]({{ site.baseurl }}/images/screenshot_config_service.png)

### Configuring Storage Servers

In the Storage Servers tab, the administrator can record the known list of DICOM storage services. These are also called _move destinations_: Dicoogle will only admit C-MOVE operations towards these endpoints.

![]({{ site.baseurl }}/images/screenshot_storage_servers.png)

### Configuration file

All configurations previously mentioned are stored in a single XML file.
Once Dicoogle is run at least once, you will find a file named _"server.xml"_ in the configuration folder `confs`.
Currently, some configurations can only be changed by editing this file. As an example, let us modify this instance's application entity title (AE Title).
Look for the XML element `aetitle`:

``` xml
    <dicom-services>
        <aetitle>DICOOGLE-STORAGE</aetitle>
    <dicom-services>
```

And modify it to another identifier:

``` xml
<aetitle>PERSONAL-STORAGE</aetitle>
```

The server needs to be restarted after this modification. When that is done, you will see that the server's AE Title has changed.

<div class="note unreleased">
  <h5>Migrating settings from Dicoogle 2 to Dicoogle 3</h5>
  <p>
  The structure of the server settings file <em>config.xml</em> has changed in Dicoogle 3.
  The instructions above refer to this new format.
  When migrating an existing installation,
  Dicoogle will automatically try to migrate the old settings file <em>config.xml</em>
  into the new one in <em>confs/server.xml</em>.
  </p>
</div>

<div class="note warning">
  <h5>Take extra care when modifying configuration files by hand!</h5>
  <p>An incorrect configuration may prevent Dicoogle from running entirely. Consider backing up this file before modifying it. In order to revert all changes to the defaults, simply delete the file and let Dicoogle generate it again.</p>
</div>

### Configuring Plugins

Plugins can have settings of their own as well. Unlike the settings presented so far, these are kept in their own xml file, in a _"settings"_ folder in _"Plugins"_, alongside the plugin jar files. This folder is automatically created, and plugins should automatically generate default configurations there. When configuring a plugin, one should attend to that plugin's documentation.

As an example, the file storage allows the administrator to define a root path where incoming files are stored. Let's modify this property to point to our storage directory. In _"DicoogleDir/Plugins/settings"_, assuming Dicoogle was run at least once, you will find a file named _"file-storage.xml"_. Look for the `root-dir` element:

```xml
<root-dir>/tmp</root-dir>
```

And change it to our storage:

```xml
<root-dir>/path/to/DicoogleDir/storage</root-dir>
```

As usual, the server needs to be restarted. Now the DICOM storage server can be safely enabled, and incoming C-STORE operations will save DICOM objects in it, organized as a directory tree.

### Adding and removing users

Although Dicoogle does not provide a UI for the managing of the system users, it provides a web service endpoint for creation of new users and removal of existent ones.

#### Create user

To create a new user in the system, one can perform a HTTP PUT in `/user` with `username` and `password` query strings. Example:

``` bash
curl -X POST "http://localhost:8080/user?username=johndoe&password=secret"
```

Alternatively, one may create an administrator user by adding the flag `admin` set to `true`:

``` bash
curl -X POST "http://localhost:8080/user?username=johndoe&password=secret&admin=true"
```

#### Remove user

The removal of the user is executed with the HTTP method DELETE and the parameters are passed as path query to the same endpoint ( `/users` ). Example:

``` bash
curl -X DELETE "http://localhost:8080/user/johndoe"
```

<div class="note unreleased" >
  <h5>Breaking changes in Dicoogle 3</h5>
  
  Some APIs may have changed starting from Dicoogle 3.0.0.
  For instance, creating new users was done with the PUT method instead of POST.
  Be sure to update all integration software when migrating.
  </ul>
</div>

------------------

This concludes the first chapter, where we have covered how to install Dicoogle in a machine, configure it, and use it for basic purposes. The next chapter is intended for developers wishing to further leverage the capabilities of Dicoogle using plugins.

