# Getting Started

This section describes how to get SolarNode running on a device. You will need to configure
your device as a SolarNode and associate your SolarNode with SolarNetwork.

!!! tip

	You might find it helpful to read through this entire guide before jumping in. There
	are screen shots and tips provided to help you along the way.

## Get your device ready to use

SolarNode can run on a variety of devices. To get started using SolarNode, you must download the
appropriate SolarNodeOS image for your device. SolarNodeOS is a complete operating system tailor
made for SolarNode.  Choose the SolarNodeOS image for the device you want to run SolarNode on and
then copy that image to your device media (typically an SD card).

### Choose your device

=== "Raspberry Pi"

	The [Raspberry Pi][rpi] is the best supported option for general SolarNode deployments. Models 3
	or later, Compute Module 3 or later, and Zero 2 W or later are supported. Use a tool like
	[Etcher][etcher] or [Raspberry Pi Imager](https://www.raspberrypi.com/software/) to copy the
	image to an SD card (minimum size is 2 GB, 4 GB recommended).

	[:fontawesome-solid-file-arrow-down: Download SolarNodeOS for Raspberry Pi](https://sourceforge.net/projects/solarnetwork/files/solarnode/pi/){ .md-button .md-button--primary}

=== "Orange Pi"

	The [Orange Pi][opi] models Zero and Zero Plus are supported. Use a tool like [Etcher][etcher] to copy
	the image to an SD card (minimum size is 1 GB, 4 GB recommended).

	[:fontawesome-solid-file-arrow-down: Download SolarNodeOS for Orange Pi](https://sourceforge.net/projects/solarnetwork/files/solarnode/orange-pi/){ .md-button .md-button--primary }

=== "Something Else"

	Looking for SolarNodeOS for a device not listed here? Reach out to us through
	[email](mailto:info@solarnetwork.net) or [Slack][slack] to see if we can help!

### Configure your network

SolarNode needs a network connection. If your device has an ethernet port, that is the most reliable way
to get started: just plug in your ethernet cable and off you go!

If you want to use WiFi, or would like more detailed information about SolarNode's networking
options, see the [Networking](networking.md) sections.

### Power it on

Insert your SD card (or other device media) into your device, and power it on.
While it starts up, proceed with the next steps.

## Associate your SolarNode with SolarNetwork

Every SolarNode must be associated (registered) with a SolarNetwork account. To associate a
SolarNode, you must:

 1. Log into [SolarNetwork][solaruser]
 2. Generate an _invitation_ for a new SolarNode
 3. Accept the invitation on SolarNode

### Log into SolarNetwork

If you do not already have a [SolarNetwork][solaruser] account, [register][user-reg] for one and then log in.

### Generate a SolarNode invitation

Click on the [My Nodes][my-nodes] link. You will see an **Invite New SolarNode** button, like this:

![Empty My Nodes page on SolarNetwork](../images/users/solaruser/mynodes-empty%402x.png){width=928}

Click the **Invite New SolarNode** button, then fill in and submit the form that appears and select your
time zone by clicking on the world map:

![Choose node time zone on world map](../images/users/solaruser/invite-node-choose-time-zone%402x.gif){width=582}

The generated SolarNode invitation will appear next.

![Generated SolarNode invitation](../images/users/solaruser/node-invitation%402x.png){width=928}

Select and copy the **entire** invitation. You will need to paste that into the SolarNode setup
screen in the next section.

### Accept the invitation on SolarNode

Open the SolarNode Setup app in your browser. The URL to use might be <http://solarnode/> or it
might be an IP address like `http://192.168.1.123`. See the [Networking](networking.md) section for
more information. You will be greeted with an invitation acceptance form into which you can paste
the invitation you generated in SolarNetwork. The acceptance process goes through the following steps:

 1. **Submit** the invitation in the acceptance form
 2. **Preview** the invitation details
 3. **Confirm** the invitation

=== "Acceptance form"

	First you **submit** the invitation in the acceptance form.

	![SolarNode invitation acceptance form](../images/users/associate/invitation-form%402x.png){width=878}

=== "Preview"

	Next you **preview** the invitation details.

	!!! note

		The expected **SolarNetwork Service** value shown in this step will be `in.solarnetwork.net`.

	![SolarNode invitation preview](../images/users/associate/invitation-preview%402x.png){width=878}

=== "Confirm"

	Finally, **confirm** the invitation. This step contacts SolarNetwork and completes the
	association process.

	!!! warning

		Ensure you provide a **Certificate Password** on this step, so SolarNetwork can generate
		a security certificate for your SolarNode.

	![Confirm SolarNode invitation details](../images/users/associate/invitation-verify%402x.png){width=878}

=== "Complete"

	When these steps are completed, SolarNetwork will have assigned your SolarNode a unique
	identifier known as your **Node ID**. A randomly generated SolarNode login password will have been
	generated; you are given the opportunity to easily change that if you prefer.

	![SolarNode invitation complete](../images/users/associate/invitation-complete%402x.png){width=878}

## Next steps

Learn more about the [SolarNode Setup](setup-app/index.md) app.

[etcher]: https://www.balena.io/etcher
[my-nodes]: https://data.solarnetwork.net/solaruser/u/sec/my-nodes
[opi]: http://www.orangepi.org/
[rpi]: https://www.raspberrypi.com/
[slack]: https://join.slack.com/t/solarnetwork-f8n/shared_invite/zt-1qhses7cw-FhwcUlSiYh~3KYIDouOH4w
[solaruser]: https://data.solarnetwork.net/solaruser/
[user-reg]: https://data.solarnetwork.net/solaruser/register.do
