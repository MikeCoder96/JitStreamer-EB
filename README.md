# JitStreamer EB

The sequel that nobody wanted, but everyone needed.

JitStreamer is a program to activate JIT across the far reaches of the internet.

I authored the original JitStreamer a few years ago, but Apple has since changed
how the protocol for debugging apps works. This program is a rewrite of that original
program, while using the new protocol.

Simply put, this program takes a pairing file and returns a Wireguard configuration.
That Wireguard configuration allows the device to interact with a server that will
activate JIT on the device.

## EB

What is EB? Electric Boogaloo.
[r/outoftheloop](https://www.reddit.com/r/OutOfTheLoop/comments/3o41fi/where_does_the_name_of_something2_electric/)

## Building

```bash
cargo build --release

```

It's not that deep.

## Running

1. Start [netmuxd](https://github.com/jkcoxson/netmuxd)
2. Install the pip requirements

```bash
pip install -r requirements.txt
```

3. Start [tunneld-rs](https://github.com/jkcoxson/tunneld-rs) or [tunneld](https://github.com/doronz88/pymobiledevice3)

4. Run the program

```bash
./target/release/jitstreamer-eb
```

**OR**

```bash
just run
```

5. Start the Wireguard peer

```bash
sudo wg-quick up jitstreamer
```

6. ???
7. Profit

### Variables

JitStreamer reads the following environment variables:

- ``RUNNER_COUNT`` - How many Python runners to spawn, defaults to ``5``
- ``ALLOW_REGISTRATION`` - Allows clients to register using the ``/register`` endpoint, defaults to ``1``
- ``JITSTREAMER_PORT`` - The port to bind to, defaults to ``9172``
- ``WIREGUARD_CONFIG_NAME`` - The name of the Wireguard interface, defaults to ``jitstreamer``
- ``WIREGUARD_PORT`` - The port that Wireguard listens on, defaults to ``51869``
- ``WIREGUARD_SERVER_ADDRESS`` - The address the server binds to, defaults to ``fd00::``
- ``WIREGUARD_ENDPOINT`` - The endpoint that client configs point to, defaults to ``jitstreamer.jkcoxson.com``
- ``WIREGUARD_SERVER_ALLOWED_IPS`` - The allowed IPs the server can bind to, defaults to ``fd00::/64``

### Custom VPN

If you don't want to use the built-in Wireguard manager, because you either
have your own VPN or want to use a different one, you'll have to manually
register your clients.

Run the following SQL on the ``jitstreamer.db`` sqlite file:

```sql
INSERT INTO DEVICES (udid, ip, last_used) VALUES ([udid], [ip], CURRENT_TIMESTAMP);
```

## Docker

There's a nice dockerfile that contains a Wireguard server and JitStreamer server,
all packaged and ready to go. It contains everything you need to run the server.

1. create a database
```bash
mkdir app
sqlite3 ./jitstreamer.db < ./src/sql/up.sql
```

2. build docker
```bash
sudo docker build -t jitstreamer-eb .
```

3. run docker compose
```bash
sudo docker compose up -d
```

Alternative method:
```bash
just docker-build
just docker-run
```
Detailed Step by Step Docker Compose [Guide](https://github.com/Unlearned6688/JitStreamer-EB/blob/master/jitstreamer-eb-debian-docker-instructions.md)

## License

[LICENSE.md]

## Contributing

Please do. Pull requests will be accepted after passing cargo clippy.

## Thanks

- [ny](https://github.com/nythepegasus/SideJITServer) for the Python implementation
- [pymobiledevice3](https://github.com/doronz88/pymobiledevice3)
