# Serverless Infrastructure

Run containers and expose them via Cloudflare Tunnels on GitHub Actions.

## Modes

| Mode | URL | Setup | Use Case |
|------|-----|-------|----------|
| Quick Tunnel | Random `*.trycloudflare.com` | None | PR previews, testing |
| Managed Tunnel | Your domain | Cloudflare account | Production, demos |

## Usage

### PR Preview (Dockerfile)

```yaml
- uses: jonasneves/serverless-infra/.github/actions/expose-service@main
  with:
    dockerfile: ./Dockerfile
    port: 3000
```

### PR Preview (Image)

```yaml
- uses: jonasneves/serverless-infra/.github/actions/expose-service@main
  with:
    image: ghcr.io/user/myapp:latest
    port: 3000
```

### Continuous Hosting

```yaml
- uses: jonasneves/serverless-infra/.github/actions/expose-service@main
  with:
    image: ghcr.io/user/myapp:latest
    port: 3000
    tunnel_token: ${{ secrets.CF_TUNNEL_TOKEN }}
    tunnel_url: https://app.example.com
    keep_alive: true
    duration: '5.5'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `image` | * | - | Docker image to pull and run |
| `dockerfile` | * | - | Path to Dockerfile to build and run |
| `port` | Yes | - | Port the container exposes |
| `env` | No | - | Environment variables (KEY=value, one per line) |
| `wait_time` | No | `10` | Seconds to wait for tunnel |
| `health_check` | No | - | HTTP path for health check |
| `max_health_attempts` | No | `30` | Max health check attempts |
| `tunnel_token` | No | - | Cloudflare Tunnel token (enables managed mode) |
| `tunnel_url` | No | - | Public URL (required with tunnel_token) |
| `keep_alive` | No | `false` | Monitor and restart crashed containers |
| `duration` | No | `5.5` | Hours to run (used with keep_alive) |

\* Provide either `image` or `dockerfile`, not both.

## Outputs

| Output | Description |
|--------|-------------|
| `url` | Public tunnel URL |
| `container_id` | Docker container ID |
| `mode` | `quick` or `managed` |

## Examples

- [PR Preview](examples/pr-preview.yml) - Preview deployments on pull requests
- [Continuous Hosting](examples/continuous-hosting.yml) - 24/7 hosting with auto-restart

## Managed Tunnel Setup

1. Go to [Cloudflare Zero Trust](https://one.dash.cloudflare.com/) > Networks > Tunnels
2. Create a tunnel and configure hostname to route to `http://localhost:PORT`
3. Copy the tunnel token to your repo secrets as `CF_TUNNEL_TOKEN`
4. Set `tunnel_url` to your configured hostname

## License

MIT
