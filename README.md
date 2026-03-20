# Snowflake Dappnode Package

Run a [Tor Snowflake](https://snowflake.torproject.org/) proxy on your Dappnode and help censored users around the world access the internet freely.

## What is Snowflake?

[Snowflake](https://snowflake.torproject.org/) is a **pluggable transport** developed by the [Tor Project](https://www.torproject.org/). It helps people living under censorship connect to the Tor network by disguising their traffic as ordinary video-call (WebRTC) traffic.

When you run a Snowflake proxy, your node acts as a temporary bridge: censored users connect through your proxy to reach the Tor network, while you never see or log the content of their traffic. The connection is end-to-end encrypted by Tor itself.

Key facts:

- **You are a proxy, not an exit node.** Traffic that passes through your proxy is Tor-encrypted and exits the Tor network elsewhere. You do not relay raw internet traffic.
- **Low resource usage.** A Snowflake proxy typically uses only a small amount of bandwidth and CPU.
- **No configuration required.** Once installed and running, the proxy automatically registers itself with the Tor broker and starts serving users.

More background: [Tor Project – Snowflake](https://community.torproject.org/relay/setup/snowflake/standalone/)

## How the Dappnode Package Works

This package wraps the official upstream Snowflake standalone proxy image so it can be installed and managed through the Dappnode interface.

### Installation

Install the package from the Dappnode DAppStore or by using the direct IPFS / ENS link. No manual Docker setup is needed.

### Services

| Service | Description |
|---|---|
| `snowflake-proxy` | Standalone Snowflake proxy. Registers with the Tor broker over HTTPS and accepts WebRTC peer connections from censored users. |

### Networking

Because Dappnode does not support `network_mode: host`, this package exposes a fixed UDP port range (`30000–30999`) mapped 1-to-1 to the host. 

You might get classified as a **restricted NAT** by the broker. It still works! Just works less effectively. If you want to make sure you are receiving connections, use the flag `--verbose` in EXTRA_OPTS in the configuration tab. 

If you want to improve the reachability further, you should:

1. Ensure your router forwards UDP ports **30000–30999** to your Dappnode host.
2. Ensure your host firewall (if any) allows inbound traffic on the same range.

### Configuration

The package exposes one environment variable that can be set in the Dappnode Package Config UI:

| Variable | Default | Description |
|---|---|---|
| `EXTRA_OPTS` | *(empty)* | Optional extra CLI flags passed directly to the `snowflake-proxy` binary. For example, set `--verbose` to enable verbose logging when troubleshooting. |

All available flags are documented in the [upstream repository](https://gitlab.torproject.org/tpo/anti-censorship/pluggable-transports/snowflake/-/tree/main/proxy).

### Logs

You can view the proxy logs in the Dappnode UI under **Packages → Snowflake → Logs**. With the default settings you will see periodic connection counts reported by the proxy. Add `--verbose` via `EXTRA_OPTS` for more detailed output.

## Notes

- The original `watchtower` sidecar was removed because Dappnode already manages package updates.
- The proxy uses the upstream `latest` image tag.
