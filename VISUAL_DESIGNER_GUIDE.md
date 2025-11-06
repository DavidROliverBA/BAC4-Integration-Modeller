# BAC4 Integration Modeller - Visual Designer Guide

## Overview

The BAC4 Visual Designer is a fully-featured, web-based tool that allows integration architects to visually design, document, and manage enterprise integration patterns. You can build new integrations from scratch or load existing integration JSON files for visualization and editing.

## Getting Started

### Opening the Designer

Simply open `visual-designer.html` in any modern web browser. No installation or server required!

### User Interface

The designer is organized into three main panels:

1. **Left Panel (Component Palette)**: Contains draggable components and quick actions
2. **Center Panel (Canvas)**: Visual workspace for designing integrations
3. **Right Panel (Properties)**: Detailed configuration for selected components

## Key Features

### 1. Loading Existing Integrations

#### Load from JSON File
- Click the **"📁 Load JSON"** button in the header
- Select your integration JSON file
- The designer will automatically parse and visualize:
  - All systems as nodes on the canvas
  - All integrations with connections between systems
  - Complete metadata and configurations

#### Load from Examples
Use the left panel to quickly load pre-built examples:
- **REST API Example**: CRM to ERP customer synchronization
- **Kafka Example**: Event streaming for order processing
- **File Transfer Example**: Batch file transfer for sales reports

#### Load from Templates
Click **"📑 Templates"** to choose from starter templates:
- REST API Integration
- Event Streaming (Kafka)
- File Transfer (SFTP/FTP)
- Message Queue (RabbitMQ, etc.)
- Database Synchronization

### 2. Building New Integrations

#### Adding Systems

Drag system types from the left panel onto the canvas:

- **💼 Application**: Business applications
- **🗄️ Database**: Data storage systems
- **☁️ Service**: Cloud services
- **🌐 External**: Third-party systems

After dropping a system:
1. Click on it to select
2. Configure properties in the right panel:
   - Name (required)
   - Type
   - Description
   - Version
   - Owner
   - Environment (dev/test/staging/production)

#### Adding Integration Patterns

Drag integration patterns from the left panel:

- **🔌 REST API**: HTTP/HTTPS request-response
- **📨 Event Stream**: Kafka, Kinesis, Event Hub
- **📁 File Transfer**: FTP, SFTP, cloud storage
- **💬 Messaging**: RabbitMQ, ActiveMQ, SQS
- **📦 Batch**: Scheduled batch processing

After dropping an integration:
1. Click on it to select
2. Configure in the right panel:
   - **Basic Info**: Name, description, pattern
   - **Source**: System, protocol, format
   - **Target**: System, protocol, format
   - **Trigger**: How the integration is triggered
   - **Metadata**: Owner, tags

#### Connecting Systems to Integrations

1. Select an integration
2. In the Properties panel, choose:
   - **Source System**: Where data comes from
   - **Target System**: Where data goes to
3. Visual connections will automatically appear on the canvas

### 3. Editing Components

#### Moving Components
- Click and drag any component to reposition it
- Connections automatically update

#### Editing Properties
1. Click on any system or integration
2. Use the Properties panel to edit:
   - All configuration fields
   - Required fields are marked with *
   - Tooltips provide guidance

#### Deleting Components
1. Select a component
2. Click the **"🗑️ Delete"** button in the Properties panel

### 4. Exporting Your Design

#### Export to JSON
1. Click **"💾 Export JSON"** in the header
2. File downloads automatically
3. File name based on your integration title
4. JSON is fully compliant with BAC4 schema

#### Export Format
The exported JSON includes:
- Complete integration definition
- All systems and their configurations
- All integration patterns and settings
- Transformation logic
- Error handling configuration
- Security settings
- Metadata and documentation

### 5. Validation

Click **"✅ Validate"** to check your design:
- Ensures required fields are filled
- Checks for valid system references
- Validates integration connections
- Reports any errors or warnings

### 6. Managing Your Design

#### Clear Canvas
- Click **"🗑️ Clear"** to start fresh
- Confirmation prompt prevents accidents

#### Save Work-in-Progress
- Use **"💾 Export JSON"** to save your work
- Load it later with **"📁 Load JSON"**

## Best Practices for Architects

### 1. Naming Conventions
- **Systems**: Use clear, recognizable names (e.g., "Salesforce CRM", "SAP ERP")
- **Integrations**: Describe what they do (e.g., "Customer Sync from CRM to ERP")
- Use consistent naming patterns across your organization

### 2. Organization
- **Group related systems** together on the canvas
- **Arrange integrations** between their source and target systems
- Use the **drag-to-reposition** feature for clean layouts

### 3. Documentation
- Fill in **descriptions** for all components
- Add **owner information** for accountability
- Use **tags** for categorization and searchability
- Specify **environment** correctly (dev/test/staging/production)

### 4. Configuration Details
Always specify:
- **Protocols**: http, https, kafka, sftp, etc.
- **Data formats**: json, xml, csv, avro, etc.
- **Trigger types**: schedule, event, webhook, etc.

### 5. Metadata and Governance
- Assign **owners** to all integrations
- Add **tags** for:
  - Business domains (customer, order, inventory)
  - Criticality (critical, high, medium, low)
  - Data sensitivity (pii, confidential, public)
  - Integration type (real-time, batch, near-real-time)

### 6. Version Control
- Export JSON regularly
- Store in version control (Git)
- Track changes over time
- Document major changes in the description field

## Integration Patterns Explained

### REST API
**Best for**: Real-time, synchronous data exchange
- Request-response pattern
- Typically HTTPS
- JSON or XML payloads
- Good for CRUD operations

### Event Stream
**Best for**: High-volume, real-time event processing
- Asynchronous, decoupled
- Apache Kafka, AWS Kinesis, Azure Event Hub
- Publish-subscribe pattern
- Excellent scalability

### File Transfer
**Best for**: Batch processing, large datasets
- Scheduled transfers
- FTP, SFTP, cloud storage
- CSV, XML, or other file formats
- Good for overnight/periodic jobs

### Messaging
**Best for**: Reliable, asynchronous communication
- Message queues (RabbitMQ, ActiveMQ)
- Guaranteed delivery
- Queue or topic patterns
- Good for decoupled systems

### Batch Processing
**Best for**: Large-scale data processing
- Scheduled execution
- Process data in chunks
- ETL workflows
- Data warehouse loading

### Database Sync
**Best for**: Keeping databases in sync
- Change Data Capture (CDC)
- Incremental or full sync
- Conflict resolution
- Data replication

## Advanced Features

### Data Transformation
Each integration includes transformation configuration:
- **Mapping**: Field-to-field mapping
- **Template**: JSONata, Handlebars, Jinja2
- **Script**: JavaScript, Python, Groovy

### Error Handling
Configure resilience:
- **Retry logic**: Max attempts, backoff strategy
- **Dead letter queue**: For failed messages
- **Alerting**: Email, Slack, PagerDuty
- **Fallback**: Alternative endpoints or default values

### Security
Define security requirements:
- API keys
- OAuth2 flows
- Mutual TLS
- Custom security schemes

## Keyboard Shortcuts

- **Delete**: Delete selected component (with confirmation)
- **Esc**: Deselect component
- **Ctrl/Cmd + S**: Export JSON (save)

## Troubleshooting

### Components not connecting visually?
- Ensure source and target systems are selected in the integration properties
- System IDs must match exactly

### JSON won't load?
- Verify JSON is valid (use a JSON validator)
- Check that it follows the BAC4 schema
- Look at example files for reference

### Properties not saving?
- Make sure you're clicking outside the input field or pressing Enter
- Changes save automatically as you type

### Export not working?
- Check browser permissions for downloads
- Try a different browser
- Ensure pop-ups aren't blocked

## Example Workflows

### Workflow 1: Building from Scratch
1. Open visual designer
2. Drag two systems (e.g., Application and Database) onto canvas
3. Drag an integration pattern between them
4. Configure the integration:
   - Select source system
   - Select target system
   - Set protocol and format
5. Add transformation logic
6. Validate
7. Export JSON

### Workflow 2: Modifying Existing Integration
1. Click **"📁 Load JSON"**
2. Select your integration file
3. Review the visual representation
4. Click on components to edit
5. Make changes in Properties panel
6. Validate changes
7. Export updated JSON

### Workflow 3: Using Templates
1. Click **"📑 Templates"**
2. Choose appropriate pattern
3. Customize:
   - Rename systems
   - Update configurations
   - Modify metadata
4. Add additional systems/integrations as needed
5. Export JSON

## JSON Schema Compliance

All exported JSON files comply with the BAC4 Integration Definition Schema (`schema/integration-definition.schema.json`), ensuring:
- Standardization across your organization
- Compatibility with BAC4 integration engine
- Validation against industry best practices
- Interoperability with other tools

## Tips for Large Integration Landscapes

1. **Start with core systems**: Add your most critical systems first
2. **Add integrations incrementally**: Build complexity gradually
3. **Export frequently**: Save your work regularly
4. **Use tags extensively**: Makes searching and filtering easier
5. **Document as you go**: Fill in descriptions immediately
6. **Review and validate**: Check your design before finalizing

## Support and Resources

- **Schema Documentation**: See `schema/integration-definition.schema.json`
- **Examples**: Check the `examples/` directory
- **Research Findings**: See `RESEARCH_FINDINGS.md` for pattern details
- **High-Level Design**: See `HIGH_LEVEL_DESIGN.md` for architecture

## Future Enhancements

Planned features for future versions:
- Auto-layout algorithm for optimal positioning
- Search and filter capabilities
- Multi-file management
- Collaboration features
- Export to other formats (PDF, PNG diagrams)
- Integration with API management platforms
- Real-time validation against running systems

---

**Version**: 1.0.0
**Last Updated**: 2024

For questions, issues, or feature requests, please contact your integration team or refer to the project documentation.
