![insightCloudSec](https://www.rapid7.com/globalassets/_logos/png/insightcloudsec-b-c.png)
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

    # Optional file(s) to scan (default: all files in the repository excluding the .git/ directory)
    target: ./[^.git]*
```

An example workflow may look like this:
```yaml
on:
  pull_request:
    branches:
      - master
      - main

jobs:
  ics-scan-and-upload:
    name: insightCloudSec repository scan with Github Advanced Security
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Scan the repository
        uses: rapid7/insightcloudsec-actions@v2
        with:
          api_key: ${{ secrets.ics_api_key }}
          base_url: ${{ secrets.ics_base_url }}
          config_name: AWS CIS Benchmark 1.4
      # the following is optional but recommended to surface results to Github Advanced Security
      - name: Upload the sarif report to Github Advanced Security
        if: always()
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: ics_scan.sarif
```
