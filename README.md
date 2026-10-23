# OpenOSINT MCP Server Configuration

This repository contains the OpenOSINT MCP server configuration for use with llama.cpp's native web UI.

## Installation

To install OpenOSINT with the MCP server:

```bash
pip install openosint
```

Or use uvx for a standalone installation:

```bash
uvx --from openosint openosint-mcp
```

## Available Tools

OpenOSINT provides 21 OSINT investigation tools:

1. **search_email** - Find social accounts linked to an email (noisy)
2. **search_username** - Check username presence across 400+ platforms (noisy)
3. **search_breach** - Check data breach exposure via HaveIBeenPwned
4. **search_whois** - Domain registrant and DNS info
5. **search_ip** - IP geolocation, ASN, hostname via ipinfo.io
6. **search_domain** - Subdomain enumeration (noisy)
7. **generate_dorks** - Generate targeted Google dork URLs
8. **search_paste** - Search Pastebin dumps for mentions
9. **search_phone** - Phone number carrier and location info (unverified)
10. **search_shodan** - Open ports, banners, CVEs via Shodan
11. **search_virustotal** - VirusTotal verdict from 70+ engines
12. **search_ip2location** - Enhanced IP intel (VPN/proxy/Tor detection)
13. **search_censys** - Internet-facing infrastructure and certificates
14. **search_abuseipdb** - IP abuse reputation scoring
15. **search_github** - GitHub profile and repository search
16. **search_dns** - DNS record lookups with SPF/DMARC/DKIM analysis
17. **search_rdap** - RDAP registry data (registrar, dates, nameservers)
18. **search_gdelt_geo** - Recent news geo-location via GDELT
19. **search_dorks_live** - Live Google search results for dorks
20. **scrape_url** - URL scraping with Cloudflare bypass (touches target)
21. **search_footprint** - Entity footprint detection and correlation

## MCP Server Configuration

### For llama.cpp Web UI

Create a configuration file `mcp-config.json`:

```json
{
  "mcpServers": {
    "openosint": {
      "command": "uvx",
      "args": ["--from", "openosint", "openosint-mcp"],
      "env": {
        "ANTHROPIC_API_KEY": "your-key",
        "SHODAN_API_KEY": "your-key",
        "HIBP_API_KEY": "your-key"
      }
    }
  }
}
```

Then start your llama.cpp server with:

```bash
llama-server --agent --mcp-config mcp-config.json
```

### Alternative: Direct Python Execution

If you installed OpenOSINT via pip:

```json
{
  "mcpServers": {
    "openosint": {
      "command": "openosint-mcp"
    }
  }
}
```

## Passive vs Active Tools

By default, only passive tools are available (20 tools). To enable all tools including noisy ones:

```json
{
  "mcpServers": {
    "openosint": {
      "command": "uvx",
      "args": ["--from", "openosint", "openosint-mcp", "--allow-active"]
    }
  }
}
```

Or set the environment variable:

```json
{
  "env": {
    "OPENOSINT_ALLOW_ACTIVE": "1"
  }
}
```

## Required API Keys

- **ANTHROPIC_API_KEY** - Required for AI agent functionality (or use Ollama/OpenAI-compatible)
- **SHODAN_API_KEY** - Optional, enables search_shodan tool
- **HIBP_API_KEY** - Optional, enables search_breach tool
- **IP2LOCATION_API_KEY** - Optional, enables search_ip2location tool
- **ABUSEIPDB_API_KEY** - Optional, enables search_abuseipdb tool
- **CENSYS_API_ID/CENSYS_API_SECRET** - Optional, enables search_censys tool

## Example Usage

Once configured, you can ask questions like:

- "What can you find about the domain example.com?"
- "Has this email address been in any data breaches?"
- "What's the IP address and location of this website?"
- "Are there any open ports on this server?"
- "What subdomains exist for this domain?"
- "Check the DNS records for this domain"

The MCP server will automatically select and chain the appropriate tools to answer your questions.