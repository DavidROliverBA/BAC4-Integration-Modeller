# BAC4 Integration Modeller

A comprehensive integration modeling tool that enables solution, software, and data architects to design enterprise integrations using JSON-based definitions. Supports multiple integration patterns including real-time APIs, event streaming (Kafka), message queuing (MQ), batch processing, and file transfers.

## 🎯 Overview

BAC4 Integration Modeller provides a **JSON Schema-based**, **standards-compliant** approach to defining, validating, and generating integration implementations. It adopts industry standards like **AsyncAPI**, **OpenAPI**, and **CloudEvents** while offering extensibility through plugins.

## ✨ Key Features

- 🎨 **NEW: Fully-Featured Visual Designer**: Load existing JSON or build from scratch with drag-and-drop
- **Multi-Pattern Support**: REST API, SOAP, Kafka, MQ, File Transfer, Batch, Database Sync
- **JSON-First Design**: Human-readable, Git-friendly integration definitions
- **Code Generation**: Generate implementation code for multiple platforms (Apache Camel, Spring Integration, Node.js, Python)
- **Auto Documentation**: Generate markdown, HTML, and diagram documentation
- **Validation Engine**: JSON Schema validation with custom business rules
- **Plugin Architecture**: Extend with custom patterns, generators, and validators
- **Integration Catalog**: Search, discover, and manage integrations
- **Standards-Based**: Built on AsyncAPI, OpenAPI, and CloudEvents specifications

## 🚀 Quick Start

### Option 1: Visual Designer (Recommended for Architects)

**No installation required!** Just open the visual designer:

```bash
# Simply open in your browser
open visual-designer.html
```

Then:
1. Load an example or start from a template
2. Drag systems and integrations onto the canvas
3. Configure properties
4. Validate and export JSON

See **[Visual Designer Guide](VISUAL_DESIGNER_GUIDE.md)** for detailed instructions.

### Option 2: Command Line (For Developers)

```bash
# Install the CLI
npm install -g bac4-integration-modeller

# Initialize a new integration definition
bac4 init my-integration

# Validate the definition
bac4 validate my-integration.json

# Generate implementation code
bac4 generate my-integration.json --target camel --output ./generated

# Generate documentation
bac4 docs my-integration.json --format html --output ./docs
```

## 📚 Documentation

### Core Documentation

- **[Visual Designer Guide](VISUAL_DESIGNER_GUIDE.md)**: Complete guide to using the visual designer ⭐
- **[Research Findings](RESEARCH_FINDINGS.md)**: Comprehensive research on integration standards and patterns
- **[High-Level Design](HIGH_LEVEL_DESIGN.md)**: Detailed architecture and design documentation
- **[Build Prompt](BUILD_PROMPT.md)**: Complete implementation guide with code examples
- **[JSON Schema](schema/integration-definition.schema.json)**: Integration definition schema

### Examples

- **[REST API Integration](examples/rest-api-integration.json)**: Customer data sync between CRM and ERP
- **[Kafka Event Stream](examples/kafka-event-stream.json)**: Real-time order event processing
- **[File Transfer & Batch](examples/file-transfer-batch.json)**: Daily sales report integration

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                 BAC4 Integration Modeller                    │
│                                                               │
│  ┌───────────┐  ┌──────────────┐  ┌─────────────────┐      │
│  │  Visual   │  │ JSON Editor  │  │  CLI Tool       │      │
│  │  Designer │  │              │  │                 │      │
│  └─────┬─────┘  └──────┬───────┘  └────────┬────────┘      │
│        │               │                    │               │
│        └───────────────┼────────────────────┘               │
│                        │                                    │
│  ┌────────────────────┴────────────────────────────┐       │
│  │      Integration Definition Engine              │       │
│  │  ┌──────────┐  ┌──────────┐  ┌─────────────┐  │       │
│  │  │Validator │  │Generator │  │Catalog      │  │       │
│  │  └──────────┘  └──────────┘  └─────────────┘  │       │
│  └─────────────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────────────┘
```

## 🔧 Integration Patterns

### REST API
```json
{
  "pattern": "rest-api",
  "source": {
    "protocol": "https",
    "connection": { "url": "https://api.salesforce.com" }
  },
  "target": {
    "protocol": "https",
    "connection": { "url": "https://erp.example.com/api" }
  },
  "config": {
    "method": "POST",
    "path": "/customers"
  }
}
```

### Kafka Event Stream
```json
{
  "pattern": "event-stream",
  "source": {
    "protocol": "kafka",
    "connection": { "topic": "orders.events" }
  },
  "config": {
    "provider": "kafka",
    "consumerGroup": "fulfillment-processor",
    "cloudEvents": true
  }
}
```

### File Transfer
```json
{
  "pattern": "file-transfer",
  "source": {
    "protocol": "sftp",
    "connection": { "path": "/exports/sales" }
  },
  "config": {
    "filePattern": "sales_*.csv",
    "polling": { "enabled": true, "interval": "5m" }
  }
}
```

## 🎨 Visual Designer

### 🚀 NEW: Fully-Featured Visual Designer

The BAC4 Visual Designer is now **fully featured** and production-ready! Open `visual-designer.html` in any browser to start designing integrations visually.

**Key Capabilities:**
- ✅ **Load Existing JSON**: Import and visualize existing integration definitions
- ✅ **Build from Scratch**: Drag-and-drop systems and integration patterns
- ✅ **Full Configuration**: Edit all properties through intuitive forms
- ✅ **Visual Connections**: Automatically render connections between systems
- ✅ **Templates**: Quick-start with pre-built integration templates
- ✅ **Validation**: Real-time validation against JSON schema
- ✅ **Export**: Download production-ready JSON definitions
- ✅ **Examples**: Load real-world examples with one click

**Perfect for Architects:**
- No coding required
- Intuitive drag-and-drop interface
- Visual representation of integration flows
- Comprehensive property editors
- Built-in best practices and templates

**Getting Started:**
1. Open `visual-designer.html` in your browser
2. Load an example or start from a template
3. Drag systems and integrations onto the canvas
4. Configure properties in the right panel
5. Validate and export your JSON

For detailed instructions, see **[Visual Designer Guide](VISUAL_DESIGNER_GUIDE.md)**

## 🧩 Extensibility

### Plugin System

Create custom plugins to extend functionality:

```typescript
import { PluginBase, PluginType } from '@bac4/core';

export default class CustomPatternPlugin extends PluginBase {
  metadata = {
    name: 'custom-pattern',
    version: '1.0.0',
    type: PluginType.PATTERN,
    description: 'Custom integration pattern'
  };

  async initialize(context) {
    // Register custom pattern
  }

  async execute(input) {
    // Custom logic
  }
}
```

## 🛠️ Technology Stack

- **Frontend**: React 18, TypeScript, Material-UI, React Flow
- **Backend**: Node.js 20+, NestJS, Prisma
- **Database**: PostgreSQL 15+ (with JSONB support)
- **Cache**: Redis 7+
- **Templates**: Handlebars
- **Validation**: Ajv (JSON Schema)
- **Code Generation**: Template-based with Handlebars

## 📦 Supported Code Generators

- **Apache Camel** (Java/Spring Boot)
- **Spring Integration** (Java)
- **Node.js** (Express, NestJS)
- **Python** (FastAPI, Flask)
- **MuleSoft** (XML configuration)
- **Azure Logic Apps** (ARM templates)
- **AWS Step Functions** (ASL)

## 🔐 Security

- JWT-based authentication
- Role-Based Access Control (RBAC)
- Secret management integration (Vault, AWS Secrets Manager)
- TLS 1.3 for API communication
- Credential tokenization in definitions

## 📊 Monitoring & Observability

- Integration catalog with search and filtering
- Version management and change tracking
- Dependency analysis and impact assessment
- SLA monitoring and alerting
- Integration health dashboard

## 🚢 Deployment

### Docker

```bash
docker-compose up -d
```

### Kubernetes

```bash
kubectl apply -f k8s/
```

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **AsyncAPI**: Event-driven API specification
- **OpenAPI**: REST API specification
- **CloudEvents**: Event data specification
- **Enterprise Integration Patterns**: Integration pattern catalog by Gregor Hohpe and Bobby Woolf
- **Apache Camel**: Integration framework
- **Spring Integration**: Enterprise integration framework

## 📞 Support

- **Documentation**: [docs.bac4.io](https://docs.bac4.io)
- **GitHub Issues**: [github.com/bac4/integration-modeller/issues](https://github.com/bac4/integration-modeller/issues)
- **Community**: [community.bac4.io](https://community.bac4.io)
- **Email**: support@bac4.io

## 🗺️ Roadmap

### Phase 1 (MVP) - Q1 2025
- ✅ Core engine with validation
- ✅ REST API and Kafka pattern support
- ✅ Apache Camel code generator
- ✅ CLI tool

### Phase 2 - Q2 2025
- 🔄 Web UI with visual designer
- 🔄 Integration catalog
- 🔄 User authentication

### Phase 3 - Q3 2025
- ⏳ All integration patterns
- ⏳ Additional code generators
- ⏳ Enhanced transformations

### Phase 4 - Q4 2025
- ⏳ Plugin marketplace
- ⏳ VS Code extension
- ⏳ Enterprise features

## 📈 Project Status

**Current Version**: 1.0.0 (Design Phase)

---

**Made with ❤️ by the BAC4 Team**

For detailed design documentation, see [HIGH_LEVEL_DESIGN.md](HIGH_LEVEL_DESIGN.md)

For implementation guide, see [BUILD_PROMPT.md](BUILD_PROMPT.md)
