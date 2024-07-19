# SolarSSH

The `sn-solarssh` package in SolarNodeOS provides a `solarssh` command-line tool for managing
[SolarSSH](../remote-access.md) connections.

## Show SSH public key

To view the node's public SSH key, you can execute `solarssh showkey`.

```sh
$ solarssh showkey
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIG7DWIuC2MVHy/gfD32sCayoVFpGVbZ8VXuQubmKjwyx SolarNode
```

## List SolarSSH sessions

Run `solarssh list` to view all available SolarSSH sessions.

```sh
$ solarssh list
b0ae36e0-06ae-4d3d-b34e-9bf2ca8049f1,ssh.solarnetwork.net,8022,43340
```

## View SolarSSH session status

Using the output of `solarssh list` you can view the SSH connection status of a specific SSH
session with `solarssh status`, like this:

```sh
$ solarssh -c b0ae36e0-06ae-4d3d-b34e-9bf2ca8049f1,ssh.solarnetwork.net,8022,43340 status
active
```

## Stop SolarSSH session

You can force a SolarSSH session to end using `solarssh stop`, like this:

```sh
$ solarssh -c b0ae36e0-06ae-4d3d-b34e-9bf2ca8049f1,ssh.solarnetwork.net,8022,43340 stop
```
