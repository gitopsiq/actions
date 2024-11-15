# Version Bump and Release Action

A GitHub Action for automated version management and release creation with AI-powered release notes generation using Azure OpenAI.

## Features

- Automatic version bumping based on commit messages
- Version history validation
- Git tag creation and management
- AI-powered release notes generation
- Configurable GitHub release creation
- Support for Azure OpenAI authentication via API key or managed identity

## Usage

```yaml
- uses: your-org/version-bump-release@v1
  with:
    azure_openai_endpoint: ${{ secrets.AZURE_OPENAI_ENDPOINT }}
    deployment_name: your-deployment-name
    # Optional API key authentication
    azure_openai_key: ${{ secrets.AZURE_OPENAI_KEY }}
    # Optional Azure OIDC federation
    azure_client_id: ${{ secrets.AZURE_CLIENT_ID }}
    azure_tenant_id: ${{ secrets.AZURE_TENANT_ID }}
    azure_subscription_id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

## Version Management

The action determines version bumps based on commit messages:
- `major` or `breaking change`: Bumps major version (1.0.0 → 2.0.0)
- `feat` or `minor`: Bumps minor version (1.1.0 → 1.2.0)
- `fix` or `patch`: Bumps patch version (1.1.1 → 1.1.2)
- Default: Patch bump

## Input Parameters

### Required Inputs

| Input | Description |
|-------|-------------|
| `azure_openai_endpoint` | Azure OpenAI service endpoint URL |
| `deployment_name` | Azure OpenAI deployment name |

### Authentication Options

| Input | Description |
|-------|-------------|
| `azure_openai_key` | Azure OpenAI API key (optional if using managed identity) |
| `azure_client_id` | Azure Client ID for OIDC federation |
| `azure_tenant_id` | Azure Tenant ID for OIDC federation |
| `azure_subscription_id` | Azure Subscription ID for OIDC federation |

### Release Configuration

| Input | Default | Description |
|-------|---------|-------------|
| `draft` | `true` | Creates release in draft state |
| `prerelease` | `true` | Marks release as pre-release |
| `release_prefix` | `"Release"` | Prefix for release titles |
| `validate_version_history` | `true` | Enables version history validation |

### AI Model Configuration

| Input | Default | Description |
|-------|---------|-------------|
| `api_version` | `"2024-02-15-preview"` | Azure OpenAI API version |
| `temperature` | `0.2` | Controls randomness in text generation (0.0-2.0). For release notes generation 0.2 is a good value, as it allows for some creativity and diversity in the generated text, while still maintaining a level of coherence and relevance. |
| `max_tokens` | `4096` | Maximum tokens in the response. For release notes generation, 4096 tokens is more than sufficient for comprehensive documentation of changes, aprproximatly 3000 - 3500 words plain English.|
| `top_p` | `1.0` | Controls diversity of output - lower values (e.g., 0.1) make responses more focused, higher values allow more diversity. For release notes generation, a value of 1.0 is appropriate, as it allows for a wide range of possible topics and styles in the generated text. |
| `frequency_penalty` | `0.0` | Reduces repetition (-2.0 to 2.0) - positive values decrease repetition, negative values may increase focus. For release notes generation, a value of 0.0 is appropriate, as it allows for natural variations in language and style, while still ensuring that the generated text is coherent and relevant. |
| `presence_penalty` | `0.0` | Influences topic diversity (-2.0 to 2.0) - positive values encourage new topics, negative values increase focus. For release notes generation, a value of 0.0 is appropriate, as it allows for a wide range of possible topics and styles in the generated text, while still ensuring that the generated text is coherent and relevant. |
| `response_format` | `"text"` | Response format (`text` or `json_object`) |
| `seed` | `""` | Random seed for deterministic outputs. Leave empty for randomness. |

## Examples

### Basic Usage with API Key

```yaml
name: Release

on:
  push:
    branches: [ main ]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - uses: your-org/version-bump-release@v1
        with:
          azure_openai_endpoint: ${{ secrets.AZURE_OPENAI_ENDPOINT }}
          azure_openai_key: ${{ secrets.AZURE_OPENAI_KEY }}
          deployment_name: gpt-4
          draft: false
          prerelease: false
```

### Using Azure Managed Identity

```yaml
name: Release

on:
  push:
    branches: [ main ]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - uses: your-org/version-bump-release@v1
        with:
          azure_openai_endpoint: ${{ secrets.AZURE_OPENAI_ENDPOINT }}
          azure_client_id: ${{ secrets.AZURE_CLIENT_ID }}
          azure_tenant_id: ${{ secrets.AZURE_TENANT_ID }}
          azure_subscription_id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          deployment_name: gpt-4
```

## Regenerating Release Notes

To get alternative release notes for a draft release:

1. Comment `/regenerate-notes` on the release PR
2. Workflow will:
   - Generate new notes with random seed
   - Update draft release
   - Comment with used seed
3. Repeat if needed

Note: Only works on draft releases and requires proper permissions.

## License

[MIT License](LICENSE)