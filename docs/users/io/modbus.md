# Modbus

SolarNode supports Modbus integration through serial port (RTU) and TCP networks.

This integration is provided by the [Modbus Communication Support (Nifty Modbus)][nifty] and
[Modbus Communication Support - Serial (Nifty Modbus JSC)][nifty-jsc] plugins, which are included in the
[solarnode-app-io-modbus-nifty-jsc][nifty-pkg] package in SolarNodeOS.

## Use

Once installed, new **Modbus serial connection** and **Modbus TCP connection** components will
appear on the [Settings > Components][components] page on your SolarNode. Click on the **Manage**
button to configure networks. You'll need to add one configuration for each Modbus network you want
to collect data from.

## Serial (RTU)

The Modbus serial connection allows you to connect to a Modbus RTU server on a serial port.

<figure markdown>
  ![Modbus serial connection settings](../../images/users/io/modbus-rtu-settings@2x.png){width=870 loading=lazy}
</figure>

Each configuration contains the following overall settings:

| Setting            | Description |
|:-------------------|:------------|
| Service Name       | A unique name to identify this network with. Other components will refer to this name to make use of the connection. |
| Serial port        | The device port name. This varies by operating system. Some examples are `/dev/ttyUSB0` and `COM1`. |
| Baud               | The maximum communication speed to use, for example `9600`. |
| Data bits          | The number of data bits per message. Must be an integer between `5` and `8`. |
| Stop bits          | The stop bits value to use. May be one of `1`, `2`, or `3` (where `3` equals 1.5 stop bits). |
| Parity             | The parity to use. May be one of `none`, `even`, or `odd`. |
| Receive timeout    | The number of milliseconds to wait for a response. |
| Flow control in    | The input flow control to use. May be one of `none`, `xon/xoff in`, or `rts/cts in`. |
| Flow control out   | The output flow control to use. May be one of `none`, `xon/xoff out`, or `rts/cts out`. |
| RS-485 Mode.       | Toggle RS-485 mode on the serial device. This is **not** required for all RS-485 networks (USB adapters generally do not require this, for example). When enabled then the other RS-485 settings are activated as well. |
| RS-485 RTS High    | Enable to set the RTS line high (to 1) when transmitting. Only applicable when **RS-485 Mode** is enabled. |
| RS-485 Termination | Toggle RS-485 bus termination. Only applicable when **RS-485 Mode** is enabled. |
| RS-485 Echo        | Enable to enable receive during transmit, typically resulting in transmitted data _echoing_ back as received data. Only applicable when **RS-485 Mode** is enabled. |
| RS-485 Before Send Delay | A number of **microseconds** to wait after enabling transmit mode before sending data. Only applicable when **RS-485 Mode** is enabled. |
| RS-485 After Send Delay | A number of **microseconds** to wait after sending data before disabling transmit mode. Only applicable when **RS-485 Mode** is enabled. |
| Port lock timeout  | The maximum number of seconds to wait to acquire exclusive use of the configured port. Multiple components can use the same port, but only one at a time. If one component is using the port and another tries to, the second component will wait at most this many seconds for the first component to finish using the port before giving up. |
| Keep Open          | The number of seconds to keep the connection open for reuse by multiple Modbus transactions. Set to `0` to open and close the connection for each transaction. |
| Reply Timeout      | The maximum amount of time to wait for a Modbus message reply, in milliseconds. |
| Send Delay         | A minimum delay to introduce between Modbus messages, in milliseconds. Some devices cannot handle messages at a high rate. Configure a positive integer to force a minimum delay between successive messages. |
| Max Ports          | The maximum number of serial ports that can be opened at once, or `0` for no limit. |
| Wire Logging       | Toggle wire-level message logging. `TRACE` level [logging](../setup-app/settings/logging.md) must also be enabled for `the net.solarnetwork.io.modbus.X` log name. |
| CLI Publishing     | Toggle [command line interface messages](../setup-app/tools/command-console.md) to help troubleshoot Modbus issues. |

## TCP

The Modbus TCP connection allows you to connect to a Modbus server on a specific IP address (or hostname)
and port number.

<figure markdown>
  ![Modbus TCP connection settings](../../images/users/io/modbus-tcp-settings@2x.png){width=870 loading=lazy}
</figure>

Each configuration contains the following overall settings:

| Setting            | Description |
|:-------------------|:------------|
| Service Name       | A unique name to identify this network with. Other components will refer to this name to make use of the connection. |
| Host               | The host name or IP address to connect to. |
| Port               | The port to connect on. |
| Port lock timeout  | The maximum number of seconds to wait to acquire exclusive use of the configured port. Multiple components can use the same port, but only one at a time. If one component is using the port and another tries to, the second component will wait at most this many seconds for the first component to finish using the port before giving up. |
| Keep Open          | The number of seconds to keep the connection open for reuse by multiple Modbus transactions. Set to `0` to open and close the connection for each transaction. |
| Reply Timeout      | The maximum amount of time to wait for a Modbus message reply, in milliseconds. |
| Send Delay         | A minimum delay to introduce between Modbus messages, in milliseconds. Some devices cannot handle messages at a high rate. Configure a positive integer to force a minimum delay between successive messages. |
| Max Threads        | The maximum number of client network threads to support, or `0` for no limit. For some networks this may limit the number of ports that can be opened. |
| Wire Logging       | Toggle wire-level message logging. `TRACE` level [logging](../setup-app/settings/logging.md) must also be enabled for `the net.solarnetwork.io.modbus.X` log name. |
| CLI Publishing     | Toggle [command line interface messages](../setup-app/tools/command-console.md) to help troubleshoot Modbus issues. |

[components]: ../setup-app/settings/components.md
[nifty]: https://github.com/SolarNetwork/solarnetwork-node/tree/develop/net.solarnetwork.node.io.modbus.nifty
[nifty-jsc]: https://github.com/SolarNetwork/solarnetwork-node/tree/develop/net.solarnetwork.node.io.modbus.nifty.jsc
[nifty-pkg]: https://github.com/SolarNetwork/solarnode-os-packages/tree/develop/solarnode-app-io-modbus-nifty-jsc/debian
