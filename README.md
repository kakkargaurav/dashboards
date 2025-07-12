# Grafana Dashboards Repository

This repository contains Grafana dashboard configurations stored as JSON files with automated synchronization to Grafana instances using GitHub Actions.

## 📁 Repository Structure

```
├── .github/workflows/
│   └── sync-dashboards.yml          # GitHub Actions workflow for dashboard sync
├── dashboards/
│   ├── DSM918+-1752302186913.json   # DSM918+ monitoring dashboard
│   ├── Ubuntu-R630-1752302225577.json  # Ubuntu R630 server dashboard
│   └── Windows-Enigma-R720-1752302240728.json  # Windows R720 server dashboard
└── README.md                        # This documentation
```

## 🚀 Features

- **Automated Sync**: Dashboards are automatically synchronized to Grafana when changes are pushed to the `main` branch
- **Scheduled Updates**: Periodic sync every 30 minutes ensures dashboards stay in sync
- **Manual Deployment**: Force sync all dashboards manually via GitHub Actions
- **Validation**: JSON validation ensures dashboard integrity before deployment
- **Error Handling**: Comprehensive error reporting and validation
- **Folder Management**: Automatically creates and manages Grafana folders

## 🔧 Setup Instructions

### 1. Repository Secrets Configuration

Configure the following secrets in your GitHub repository (`Settings` → `Secrets and variables` → `Actions`):

| Secret Name | Description | Required | Example |
|-------------|-------------|----------|---------|
| `GRAFANA_URL` | Your Grafana instance URL | ✅ | `https://grafana.example.com` |
| `GRAFANA_API_KEY` | Grafana API key with admin permissions | ✅ | `glsa_xxxxxxxxxxxxx` |
| `GRAFANA_ORG_ID` | Grafana organization ID | ❌ | `1` (default) |

### 2. Creating a Grafana API Key

1. Log into your Grafana instance as an admin
2. Go to `Administration` → `Users and access` → `Service accounts`
3. Click `Add service account`
4. Name: `GitHub-Dashboard-Sync`
5. Role: `Admin` (required for creating folders and dashboards)
6. Click `Add` then `Add service account token`
7. Copy the token and add it as the `GRAFANA_API_KEY` secret

### 3. Dashboard Management

#### Adding New Dashboards

1. Export your dashboard from Grafana:
   - Open the dashboard in Grafana
   - Click the share icon → `Export` → `Save to file`
2. Save the JSON file in the `dashboards/` directory
3. Commit and push to the `main` branch
4. The workflow will automatically sync the new dashboard

#### Updating Existing Dashboards

1. Make changes to your dashboard in Grafana (or edit the JSON directly)
2. Export the updated dashboard JSON
3. Replace the existing file in `dashboards/`
4. Commit and push changes
5. The workflow will update the dashboard in Grafana

#### Dashboard Naming Convention

Use descriptive filenames that include:
- System/service name
- Timestamp or version (optional)
- `.json` extension

Examples:
- `windows-server-monitoring.json`
- `kubernetes-cluster-overview.json`
- `application-performance-v2.json`

## 🔄 Workflow Triggers

The sync workflow runs in the following scenarios:

### 1. Push Trigger
- **When**: Changes pushed to `main` branch affecting `dashboards/**` files
- **Behavior**: Only syncs modified dashboard files
- **Use case**: Development and maintenance updates

### 2. Scheduled Trigger
- **When**: Every 30 minutes (configurable)
- **Behavior**: Syncs all dashboard files
- **Use case**: Ensures consistency and catches manual Grafana changes

### 3. Manual Trigger
- **When**: Manually triggered via GitHub Actions UI
- **Behavior**: Option to force sync all dashboards
- **Use case**: Initial setup, troubleshooting, or bulk updates

## 📊 Dashboard Sync Process

1. **Validation**: JSON structure validation for all dashboard files
2. **Connection Test**: Verifies Grafana API connectivity
3. **Folder Management**: Creates "Dashboards" folder if it doesn't exist
4. **Dashboard Import**: Imports/updates dashboards with:
   - Automatic UID generation if missing
   - Version control (removes old version info)
   - Overwrite protection for existing dashboards
5. **Summary Report**: Provides detailed sync results in GitHub Actions summary

## 🛠️ Troubleshooting

### Common Issues

#### 1. Authentication Errors
```
❌ Failed to connect to Grafana: Request failed with status code 401
```
**Solution**: Verify `GRAFANA_API_KEY` secret is correctly set and the service account has admin permissions.

#### 2. Invalid JSON
```
❌ Invalid JSON in dashboards/my-dashboard.json
```
**Solution**: Validate JSON syntax using a JSON validator or `jq` command.

#### 3. Dashboard Import Failures
```
❌ Failed to sync dashboard: Dashboard name is required
```
**Solution**: Ensure dashboard JSON contains required fields like `title`.

### Manual Sync Testing

You can test the sync locally:

```bash
# Install dependencies
npm install axios fs-extra glob

# Set environment variables
export GRAFANA_URL="https://your-grafana.com"
export GRAFANA_API_KEY="your-api-key"
export GRAFANA_ORG_ID="1"

# Run sync script (create from workflow)
node sync-dashboards.js dashboards/your-dashboard.json
```

## 🔒 Security Considerations

- **API Key Security**: Store API keys as GitHub secrets, never in code
- **Least Privilege**: Use service accounts with minimal required permissions
- **Access Control**: Restrict repository access to authorized personnel
- **Audit Trail**: All sync operations are logged in GitHub Actions

## 📈 Monitoring and Alerts

### GitHub Actions Monitoring

- Monitor workflow runs in the `Actions` tab
- Set up notifications for failed workflows
- Review sync summaries for detailed operation reports

### Grafana Monitoring

- Check dashboard versions and update timestamps
- Monitor for unexpected dashboard changes
- Verify folder organization remains consistent

## 🤝 Contributing

### Adding New Dashboards

1. Create a feature branch: `git checkout -b add-new-dashboard`
2. Add your dashboard JSON to `dashboards/`
3. Test locally if possible
4. Create a pull request with description of the dashboard purpose
5. After review and merge, the dashboard will be automatically synced

### Modifying the Workflow

1. Test workflow changes in a fork or feature branch
2. Ensure backward compatibility with existing dashboards
3. Update documentation for any new features or requirements
4. Test with a small subset of dashboards before full deployment

## 📝 Dashboard JSON Structure

Each dashboard JSON should contain:

```json
{
  "title": "Dashboard Name",
  "uid": "unique-identifier",
  "tags": ["tag1", "tag2"],
  "panels": [...],
  "templating": {...},
  "time": {...}
}
```

**Important**: The sync process automatically:
- Removes `id` and `version` fields
- Generates `uid` if missing
- Sets folder assignment
- Adds sync metadata

## 🎯 Best Practices

1. **Version Control**: Keep dashboard JSON files in version control
2. **Descriptive Names**: Use clear, descriptive dashboard titles and filenames
3. **Regular Sync**: Let the automated sync handle updates rather than manual imports
4. **Testing**: Test dashboard changes in a development Grafana instance first
5. **Documentation**: Include dashboard purpose and data sources in commit messages
6. **Backup**: The repository serves as a backup of your dashboard configurations

## 📞 Support

For issues related to:
- **Workflow failures**: Check GitHub Actions logs and this troubleshooting guide
- **Dashboard JSON**: Validate using Grafana's import functionality
- **API connectivity**: Verify Grafana instance accessibility and API key permissions

---

**Last Updated**: December 2024  
**Workflow Version**: v1.0