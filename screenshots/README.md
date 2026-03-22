# Screenshots

This folder contains screenshots captured during the SOC lab exercise (IR-2026-001).
Add your Splunk screenshots here after running the detections and loading the dashboard.

## Expected Files

| File | Description |
|---|---|
| `dashboard.png` | Full SOC Incident Response Dashboard in Splunk showing all 5 panels |
| `det001.png` | DET-001 search results — brute force failed logon table with counts by src_ip |
| `timeline.png` | Attack timeline chart showing all event types over the incident window |

## How to Capture

1. Open Splunk Web and import `dashboard/soc_incident_dashboard.xml`
2. Run each SPL query from `detections/` in Search & Reporting
3. Take screenshots of the results and save them here
4. Use descriptive filenames matching the table above

## Notes

- Screenshots should be in `.png` format
- Recommended resolution: 1920x1080 or higher
- Redact any sensitive hostnames or credentials before committing
- Screenshots are referenced in `INCIDENT_REPORT.md` under Detection Details
