![insightCloudSec](https://brand.rapid7.com/includes/file/png/insightcloudsec-b-c.png)
# insightCloudSec Scan
The [insightCloudSec Scan](https://docs.divvycloud.com/docs/iac-cli-scanning-tool) Github Action allows security and development teams to integrate infrastructure-as-code (IaC) scanning in their CI/CD pipelines.

# About insightCloudSec
[insightCloudSec](https://www.rapid7.com/products/insightcloudsec/) secures your public cloud environment from development to production with a modern, integrated, and automated approach. 

# Usage
Two secrets need to be added for the action to work:
- `ICS_BASE_URL` - The URL of your InsightCloudSec server
- `ICS_API_KEY` - An InsightCloudSec API key

Read how to set secrets here: https://docs.github.com/en/actions/security-guides/encrypted-secrets


```yaml
- uses: rapid7/insightcloudsec-actions@latest
  with:
    # "ICS_API_KEY" secret
    api_key: ${{ secrets.ics_api_key }}

    # "ICS_BASE_URL" secret
    base_url: ${{ secrets.ics_base_url }}

    # Name of the IaC config you wish to scan with
    config_name: AWS CIS Benchmark 1.4

    # Name of the scan
    scan_name: Scan on Push

    # File to scan
    target: example.yml

    # Optional driver to use when unable to autodetect (terraform|cft)
    driver: terraform
```

An example workflow may look like this:
```yaml
on:
  push:
    # triggers on pushing to non-trunk branches
    branches-ignore:    
      - master
      - main

jobs:
  ics-scan-and-upload:
    name: insightCloudSec scan and upload
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Scan cloudformation template
        uses: rapid7/insightcloudsec-actions@latest
        with:
          api_key: ${{ secrets.ics_api_key }}
          base_url: ${{ secrets.ics_base_url }}
          config_name: AWS CIS Benchmark 1.4
          scan_name: Scan on Push
          target: example.yml
      # recommended to surface results to Github Code Scanning
      - name: Upload SARIF file
        if: always()
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: scan_output.sarif
```
