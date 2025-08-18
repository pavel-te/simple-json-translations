# Translation Project

This project demonstrates automated translation processing using GitHub Actions and the PTC CLI tool.

## Project Structure

```
gh-react-app-2/
├── .github/workflows/
│   └── translations.yml          # GitHub Actions workflow for automated translations
├── locales/
│   ├── admins-en.json            # Admin panel translations (processed)
│   └── en.json                   # Main application translations (processed)
├── ignore-this-en.json           # Ignored file (NOT processed for translation)
├── root.en.json                  # Root-level translations (processed)
└── README.md                     # This file
```

## Translation Files

### Processed Files
The following English source files are automatically processed for translation:

1. **`locales/admins-en.json`** - Contains admin panel translations
   ```json
   {
     "admins": {
       "title": "Admins",
       "description": "Admins page"
     }
   }
   ```

2. **`locales/en.json`** - Main application translations
   ```json
   {
     "hello": "Hello",
     "world": "World"
   }
   ```

3. **`root.en.json`** - Root-level translations
   ```json
   {
     "hi": "Five!"
   }
   ```

### Ignored Files
- **`ignore-this-en.json`** - This file is intentionally excluded from translation processing, even though it follows the `*-en.json` pattern. It contains content that should not be translated.

## Automated Translation Workflow

The project uses GitHub Actions to automatically process translations using the [PTC CLI tool](https://github.com/OnTheGoSystems/ptc-cli/).

### How It Works

1. **Trigger**: The workflow runs automatically when:
   - Changes are pushed to `main` branches
   - Files matching `locales/*en.json` or `root.en.json` are modified
   - Manual trigger via GitHub Actions UI

2. **Translation Process**:
   - Downloads and sets up the PTC CLI tool
   - Scans for English source files using patterns: `locales/*{{lang}}.json,root.{{lang}}.json`
   - Uploads source files to PTC (Private Translation Cloud) API
   - Processes translations for multiple target languages (Defined in [PTC Dashboard](https://app.ptc.wpml.org/))
   - Downloads completed translations

3. **Output**: Creates a Pull Request with:
   - Translated files for each target language (e.g., `locales/de.json`, `locales/fr.json`, `root.de.json`)
   - Automatic commit message: "🌐 Update translations via PTC CLI"
   - Detailed PR description with trigger information

### PTC CLI Tool

The workflow utilizes the [PTC CLI](https://github.com/OnTheGoSystems/ptc-cli/) tool, which provides:

- **Flexible file processing** using glob patterns
- **API integration** with Private Translation Cloud
- **Step-based workflow**: Upload → Process → Monitor → Download
- **Progress monitoring** with status indicators
- **Error handling** suitable for CI/CD environments
- **Multiple output formats** support

#### Key Features Used:
- Pattern-based file discovery
- Bearer token authentication via environment variables (See [Environment Secrets](https://github.com/pavel-te/simple-json-translations/settings/secrets/actions))
- Verbose logging for debugging
- Automatic language code substitution (`{{lang}}` placeholder)

## Configuration

### Environment Variables
- `PTC_API_TOKEN` - Required secret for PTC API authentication

### Workflow Permissions
The GitHub Actions workflow requires:
- `contents: write` - To create commits with translated files
- `pull-requests: write` - To create pull requests


### Github Actions Pull-Request permissions

You need to enable "Allow GitHub Actions to create and approve pull requests" - [Github Actions Permissions](https://docs.github.com/en/actions/security-for-github-actions/security-for-github-actions#permissions-for-the-github_token). Otherwise, the workflow will fail with the following error:
```
Error: You are not authorized to create a pull request.
```

## Development

To test the translation workflow locally:

1. Set up your PTC API token:
   ```bash
   export PTC_API_TOKEN=your-secret-token
   ```

2. Download and run PTC CLI:
   ```bash
   curl -fsSL https://raw.githubusercontent.com/OnTheGoSystems/ptc-cli/refs/heads/main/ptc-cli.sh -o ptc-cli.sh
   chmod +x ptc-cli.sh
   ./ptc-cli.sh --source-locale en --patterns "locales/*{{lang}}.json,root.{{lang}}.json" --api-token="$PTC_API_TOKEN" --verbose
   ```

## Notes

- The `ignore-this-en.json` file demonstrates how certain files can be excluded from translation by not matching the configured patterns
- All translated files are automatically committed and submitted as pull requests
- Translation status is monitored with real-time progress indicators
