## Transitioning from Docker

Docker is a new containerization technology built on top of the `LXC` kernel containment system, a component of the Linux OS. While there are some similarities between the Docker and Spoon platforms, there are also significant differences.

This section summarizes some of the substantial differences for those that are familiar with Docker.

### Platform

Spoon was designed for the Windows platform and its containerization system is built on top of the Spoon application virtualization engine. Docker was designed for use on Linux environments and is built on top of the LXC application virtualization system. Put another way, the Spoon VM plays the same role for Spoon containers as LXC does for Docker.

For more information about the differences between application and OS virtualization, see the article [How Spoon is different from hardware virtualization](/docs/getting-started/what-is-spoon#how-does-it-work).

Via the Spoon VM, Spoon containers support many Windows-specific constructs, such as Windows Services, COM/DCOM components, named kernel object isolation, WinSxS side-by-side versioning, shell registration, clipboard data, and other mechanisms that do not directly apply to Linux operating systems. 

Spoon also provides a desktop client with many features (GUI tool to launch applications, file extension associations, Start Menu integration) that allow containerized applications to interact with the user in the same way as traditionally installed desktop applications. Spoon also provides a small browser plugin that allows users to launch and stream containerized applications directly from any web browser.

### Layering

Spoon containers are designed to operate in a *layered* virtual application engine architecture. Layers can be thought of as "transparent sheets" of virtual environment configuration that can be stacked on top of one another to build many distinct configurations out of discrete components.

For example, to build a container for a Java application that uses a MongoDB database, a Spoon user could combine a Java runtime layer with a MongoDB database layer, then stack the application code and content in an application layer on top of its dependency layers. Layers make it extremely easy to re-use shared components such as runtimes, databases, and plugins.

Layers can also be used to apply application configuration information. For example, one might have a layer that specifies the default homepage, favorites, and security settings for a browser. This can be applied on top of a base browser layer to impose those settings onto a non-customized browser environment.

Layers can be applied dynamically and programmatically, so you can present distinct application configurations to specific groups of users without rebuilding the base application container. Continuing the browser example, one might create a base browser layer with a particular configuration layer for the development team, then use the same base browser with a different configuration layer for the sales team.

Spoon layering also enables *partial rollback* during container builds via the `using` command. This is especially useful when an external tool is required *during* the build process, but is *not* desired in the ultimate container. For example:

    using git
	// pull in stuff using git
	
	// use the stuff to complete container setup

In this code, the contents of the `git` container will *not* be present in the output container. By contrast, Docker does not distinguish between content that is imported for use only during the build process and content required by the application container at runtime.

#### Multi-base image support

Layering can be used in the `spoon.me` automated build script to build containers on top of *multiple* base images. For example, in SpoonScript the following is valid:

    from java, mongodb, redis

By contrast, Docker does *not* support creation of images from multiple base images. In other words, Spoon supports "multiple inheritance" through source layering, and "polymorphism" through post-layering.

### Streaming

Spoon, like Docker, supports the use of local containers and the ability to push and pull containers from a central
repository.

In addition, Spoon provides the ability to efficiently *stream* containers over the Internet. The Spoon system includes a predictive streaming engine to launch containers efficiently over wide area networks (WANs) without requiring the endpoint to download the entire VM image. Because many applications can be multiple gigabytes in size, this prevents a large startup latency for remote end users. Spoon predictive streaming uses statistical techniques to predict application resource access patterns based on profiles of previous user interactions. Spoon then adapts the stream data flow based on predictions of subsequent required resources.

Spoon provides a fully hosted application hosting service at [Turbo.net](http://turbo.net), which hosts application streams, provides application and user portals, automatically synchronizes user settings and application state to the cloud, and provides file synchronization and shared folders. For more information, visit the [Turbo.net homepage](http://turbo.net).

### Variable Isolation

Unlike Docker, Spoon containers are *not* required to be completely isolated from the host device resources. Spoon can fully or partially isolate objects as needed at a fine granularity.

A Spoon **isolation mode** may be specified on a per-object basis. For example, it is possible to specify that one directory subtree should be fully isolated while another one is visible from the host device. Supported isolation modes include **Full**, **Merge**, **Hide**, and **Write Copy**.

For more information on isolation modes, please see the [Isolation Modes](/docs/building/working-with-spoon-studio#virtual-filesystem) section of this documentation.

### Networking

By default, all Spoon container ports are exposed on the host device network adapter, just as if the containerized application had been executed natively. If desired, all ports can be closed with a single command and then reopened
or remapped on an individual basis. Like Docker, Spoon supports mapping of TCP, UDP, and DNS, as well as container-level remapping and IPC linking.

Importantly, this contrasts with Docker, which *isolates* all ports by default. With Spoon, a server container will work by default on the host device (ports open) and requires a separate command to close or relink the ports. Closing ports may be desired for various reasons, such as minimizing the network surface area in production environments.

### Toolchain

Like Docker, Spoon provides command-line interfaces (`spoon`) and a scripting language (**SpoonScript**) for automating build processes. Spoon also provides a number of rich GUI- and web-based tools and services for building, configuring, and managing virtual environments.

**[Spoon Studio](/docs/building/working-with-spoon-studio)** is a graphical integrated development environment that provides a visual design environment and easy-to-use wizards for creating images. Spoon Studio also includes a [Desktop Scan](/docs/building/working-with-spoon-studio#desktop-scan) tool that automatically detects and captures settings for any applications that are locally installed on a desktop.

**[Spoon Server](http://spoon.net/server)** is an on-premises version of Spoon.net that	provides the same functionality in a behind-firewall environment. In addition, Spoon Server provides enterprise-specific such as a web application portal, Active Directory and LDAP integration, centralized management, user data synchronization, license management, and usage analytics.

Spoon also maintains an online database of validated application templates and images for thousands of popular
software applications.

### Configuration

In addition to dynamic configuration via a console or script, Spoon also supports configuration via a static XML-based specification that declares the files, registry keys, environment variables, and other virtual machine states that will be presented to the container. 

To assist in building static configurations, Spoon offers a graphical **Spoon Studio** interface, as well as both a graphical- and command line-based *snapshot* configuration.

For more information on Spoon Studio and snapshot tools, see the [Building](/docs/building) section in this documentation.

### Partners

Spoon technology is integrated into select third-party enterprise application delivery platforms, including [Novell ZENworks Suite](https://www.novell.com/products/zenworks/zenworks-suite/), [Novell ZENworks Application Virtualization](http://novell.com/zav), and [LANDesk Management Suite](http://landesk.com).
