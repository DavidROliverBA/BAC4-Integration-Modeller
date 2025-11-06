# BAC4 Integration Modeller - Build Prompt

## Overview
This document provides comprehensive instructions for building the BAC4 Integration Modeller, a tool that enables architects to design integrations using JSON-based definitions. Use this prompt to guide the implementation process.

---

## Project Description

**Name**: BAC4 Integration Modeller

**Purpose**: A comprehensive integration modeling tool that allows solution, software, and data architects to design enterprise integrations (real-time, batch, file transfer, API, MQ, Kafka) using JSON-based definitions that adopt existing standards like AsyncAPI, OpenAPI, and CloudEvents.

**Key Objectives**:
1. Provide a JSON Schema-based format for defining integrations
2. Support multiple integration patterns (REST, Kafka, MQ, File Transfer, Batch, SOAP, Database Sync)
3. Enable visual and declarative integration design
4. Generate implementation code for multiple platforms
5. Auto-generate documentation and diagrams
6. Provide extensibility through plugins

---

## Implementation Phases

### Phase 1: Foundation & Core Engine (MVP)

#### 1.1 Project Setup

**Create project structure:**
```
bac4-integration-modeller/
├── packages/
│   ├── core/                 # Core engine
│   ├── cli/                  # CLI tool
│   ├── web-ui/              # React web UI
│   ├── api/                 # Backend API
│   └── shared/              # Shared types and utilities
├── schema/                   # JSON Schema definitions
├── templates/               # Code generation templates
├── plugins/                 # Plugin examples
├── examples/                # Example integration definitions
├── docs/                    # Documentation
└── scripts/                 # Build and deployment scripts
```

**Initialize monorepo:**
- Use pnpm workspaces or Nx monorepo
- Set up TypeScript configuration (tsconfig.json)
- Configure ESLint and Prettier
- Set up Jest for testing

**Technology choices:**
- **Language**: TypeScript (Node.js 20+)
- **Build Tool**: esbuild or Vite
- **Package Manager**: pnpm
- **Testing**: Jest + Supertest
- **Linting**: ESLint + Prettier

#### 1.2 JSON Schema Implementation

**File**: `schema/integration-definition.schema.json`

**Tasks**:
1. Use the provided JSON Schema as the foundation
2. Validate the schema using JSON Schema validator
3. Create TypeScript types from schema using `json-schema-to-typescript`
4. Document all schema properties with examples
5. Create schema version management system

**Generated types location**: `packages/shared/src/types/integration-definition.ts`

**Testing**:
- Unit tests for schema validation
- Test cases for valid and invalid definitions
- Edge case testing (optional fields, references, etc.)

#### 1.3 Core Engine (`packages/core`)

**Module**: `IntegrationDefinitionParser`

```typescript
// packages/core/src/parser/integration-definition-parser.ts

import Ajv from 'ajv';
import addFormats from 'ajv-formats';
import { IntegrationDefinition } from '@bac4/shared';

export class IntegrationDefinitionParser {
  private ajv: Ajv;

  constructor() {
    this.ajv = new Ajv({ allErrors: true, strict: false });
    addFormats(this.ajv);
  }

  /**
   * Parse integration definition from JSON string
   */
  parse(json: string): IntegrationDefinition {
    const data = JSON.parse(json);
    return this.parseObject(data);
  }

  /**
   * Parse integration definition from object
   */
  parseObject(data: any): IntegrationDefinition {
    // Validate against schema
    const valid = this.validate(data);
    if (!valid) {
      throw new Error('Invalid integration definition');
    }
    return data as IntegrationDefinition;
  }

  /**
   * Validate integration definition
   */
  validate(data: any): boolean {
    const schema = require('../../../schema/integration-definition.schema.json');
    const validate = this.ajv.compile(schema);
    return validate(data);
  }

  /**
   * Get validation errors
   */
  getErrors() {
    return this.ajv.errors;
  }

  /**
   * Resolve $ref references in the definition
   */
  async resolveReferences(definition: IntegrationDefinition): Promise<IntegrationDefinition> {
    // Implement reference resolution
    // Handle both internal (#/components/schemas/...) and external references
    return definition;
  }
}
```

**Module**: `ValidationEngine`

```typescript
// packages/core/src/validation/validation-engine.ts

import { IntegrationDefinition, ValidationResult, ValidationError } from '@bac4/shared';

export class ValidationEngine {
  /**
   * Validate integration definition at multiple levels
   */
  async validate(definition: IntegrationDefinition): Promise<ValidationResult> {
    const errors: ValidationError[] = [];

    // 1. Schema validation (already done by parser)

    // 2. Semantic validation
    errors.push(...this.validateReferences(definition));
    errors.push(...this.validateCircularDependencies(definition));
    errors.push(...this.validateBusinessRules(definition));

    // 3. Best practices (warnings)
    const warnings = this.lint(definition);

    return {
      valid: errors.length === 0,
      errors,
      warnings
    };
  }

  /**
   * Validate that all references exist
   */
  private validateReferences(definition: IntegrationDefinition): ValidationError[] {
    const errors: ValidationError[] = [];

    // Check system references
    for (const integration of definition.integrations) {
      if (!definition.systems[integration.source.system]) {
        errors.push({
          path: `integrations[${integration.id}].source.system`,
          message: `System "${integration.source.system}" not found in systems dictionary`,
          code: 'INVALID_REFERENCE'
        });
      }
      if (!definition.systems[integration.target.system]) {
        errors.push({
          path: `integrations[${integration.id}].target.system`,
          message: `System "${integration.target.system}" not found in systems dictionary`,
          code: 'INVALID_REFERENCE'
        });
      }
    }

    return errors;
  }

  /**
   * Check for circular dependencies
   */
  private validateCircularDependencies(definition: IntegrationDefinition): ValidationError[] {
    // Implement cycle detection algorithm
    return [];
  }

  /**
   * Validate business rules
   */
  private validateBusinessRules(definition: IntegrationDefinition): ValidationError[] {
    const errors: ValidationError[] = [];

    // Example: REST API must have HTTP/HTTPS protocol
    for (const integration of definition.integrations) {
      if (integration.pattern === 'rest-api') {
        if (!['http', 'https'].includes(integration.source.protocol)) {
          errors.push({
            path: `integrations[${integration.id}].source.protocol`,
            message: 'REST API pattern requires HTTP or HTTPS protocol',
            code: 'BUSINESS_RULE_VIOLATION'
          });
        }
      }
    }

    return errors;
  }

  /**
   * Lint for best practices
   */
  lint(definition: IntegrationDefinition): ValidationError[] {
    const warnings: ValidationError[] = [];

    // Check if descriptions are provided
    for (const integration of definition.integrations) {
      if (!integration.description) {
        warnings.push({
          path: `integrations[${integration.id}].description`,
          message: 'Integration should have a description',
          code: 'MISSING_DESCRIPTION',
          severity: 'warning'
        });
      }
    }

    return warnings;
  }
}
```

**Module**: `CodeGenerator`

```typescript
// packages/core/src/generation/code-generator.ts

import Handlebars from 'handlebars';
import { IntegrationDefinition, GenerationTarget, GeneratedArtifacts } from '@bac4/shared';

export class CodeGenerator {
  private templateEngine: typeof Handlebars;
  private generators: Map<string, Generator>;

  constructor() {
    this.templateEngine = Handlebars;
    this.generators = new Map();
    this.registerHelpers();
    this.loadGenerators();
  }

  /**
   * Generate code from integration definition
   */
  async generate(
    definition: IntegrationDefinition,
    target: GenerationTarget
  ): Promise<GeneratedArtifacts> {
    const generator = this.generators.get(target.platform);
    if (!generator) {
      throw new Error(`Generator not found for platform: ${target.platform}`);
    }

    return generator.generate(definition, target);
  }

  /**
   * List supported generation targets
   */
  listSupportedTargets(): GenerationTarget[] {
    return Array.from(this.generators.values()).map(g => g.getTarget());
  }

  /**
   * Register custom Handlebars helpers
   */
  private registerHelpers() {
    // camelCase helper
    this.templateEngine.registerHelper('camelCase', (str: string) => {
      return str.replace(/[-_\s](.)/g, (_, c) => c.toUpperCase());
    });

    // pascalCase helper
    this.templateEngine.registerHelper('pascalCase', (str: string) => {
      const camel = str.replace(/[-_\s](.)/g, (_, c) => c.toUpperCase());
      return camel.charAt(0).toUpperCase() + camel.slice(1);
    });

    // snake_case helper
    this.templateEngine.registerHelper('snakeCase', (str: string) => {
      return str.replace(/[A-Z]/g, (c) => `_${c.toLowerCase()}`);
    });

    // ifEquals helper
    this.templateEngine.registerHelper('ifEquals', function(arg1, arg2, options) {
      return (arg1 === arg2) ? options.fn(this) : options.inverse(this);
    });

    // json helper
    this.templateEngine.registerHelper('json', (obj: any, indent?: number) => {
      return JSON.stringify(obj, null, indent || 0);
    });
  }

  /**
   * Load built-in generators
   */
  private loadGenerators() {
    // Register generators
    this.generators.set('camel', new CamelGenerator(this.templateEngine));
    this.generators.set('spring-integration', new SpringIntegrationGenerator(this.templateEngine));
    this.generators.set('nodejs', new NodeJsGenerator(this.templateEngine));
  }
}

/**
 * Base generator class
 */
abstract class Generator {
  constructor(protected templateEngine: typeof Handlebars) {}

  abstract getTarget(): GenerationTarget;
  abstract generate(definition: IntegrationDefinition, target: GenerationTarget): Promise<GeneratedArtifacts>;

  protected loadTemplate(templatePath: string): HandlebarsTemplateDelegate {
    const fs = require('fs');
    const templateSource = fs.readFileSync(templatePath, 'utf-8');
    return this.templateEngine.compile(templateSource);
  }
}

/**
 * Apache Camel generator
 */
class CamelGenerator extends Generator {
  getTarget(): GenerationTarget {
    return {
      platform: 'camel',
      version: '4.0',
      language: 'java',
      framework: 'spring-boot'
    };
  }

  async generate(definition: IntegrationDefinition, target: GenerationTarget): Promise<GeneratedArtifacts> {
    const artifacts: GeneratedArtifacts = {
      files: [],
      dependencies: [],
      documentation: ''
    };

    // Generate route builder for each integration
    for (const integration of definition.integrations) {
      const template = this.loadTemplate('./templates/camel/RouteBuilder.java.hbs');
      const code = template({ integration, definition });

      artifacts.files.push({
        path: `src/main/java/com/example/routes/${integration.id}Route.java`,
        content: code,
        language: 'java'
      });
    }

    // Generate pom.xml
    const pomTemplate = this.loadTemplate('./templates/camel/pom.xml.hbs');
    artifacts.files.push({
      path: 'pom.xml',
      content: pomTemplate({ definition }),
      language: 'xml'
    });

    // Add dependencies
    artifacts.dependencies = [
      { name: 'camel-spring-boot-starter', version: '4.0.0' },
      { name: 'camel-http', version: '4.0.0' },
      { name: 'camel-kafka', version: '4.0.0' }
    ];

    return artifacts;
  }
}
```

**Module**: `DocumentationGenerator`

```typescript
// packages/core/src/documentation/documentation-generator.ts

import { IntegrationDefinition } from '@bac4/shared';

export class DocumentationGenerator {
  /**
   * Generate Markdown documentation
   */
  generateMarkdown(definition: IntegrationDefinition): string {
    let md = `# ${definition.info.title}\n\n`;
    md += `Version: ${definition.info.version}\n\n`;

    if (definition.info.description) {
      md += `${definition.info.description}\n\n`;
    }

    md += `## Systems\n\n`;
    for (const [id, system] of Object.entries(definition.systems)) {
      md += `### ${system.name}\n\n`;
      md += `- **ID**: ${id}\n`;
      md += `- **Type**: ${system.type}\n`;
      if (system.description) {
        md += `- **Description**: ${system.description}\n`;
      }
      md += `\n`;
    }

    md += `## Integrations\n\n`;
    for (const integration of definition.integrations) {
      md += `### ${integration.name}\n\n`;
      md += `- **ID**: ${integration.id}\n`;
      md += `- **Pattern**: ${integration.pattern}\n`;
      md += `- **Source**: ${definition.systems[integration.source.system].name}\n`;
      md += `- **Target**: ${definition.systems[integration.target.system].name}\n`;

      if (integration.description) {
        md += `\n${integration.description}\n`;
      }

      md += `\n**Data Flow:**\n\n`;
      md += `\`\`\`\n`;
      md += `${definition.systems[integration.source.system].name} `;
      md += `--[${integration.pattern}]--> `;
      md += `${definition.systems[integration.target.system].name}\n`;
      md += `\`\`\`\n\n`;
    }

    return md;
  }

  /**
   * Generate Mermaid diagram
   */
  generateMermaidDiagram(definition: IntegrationDefinition): string {
    let mermaid = 'graph LR\n';

    // Add systems as nodes
    for (const [id, system] of Object.entries(definition.systems)) {
      mermaid += `  ${id}["${system.name}"]\n`;
    }

    // Add integrations as edges
    for (const integration of definition.integrations) {
      mermaid += `  ${integration.source.system} -->|${integration.pattern}| ${integration.target.system}\n`;
    }

    return mermaid;
  }

  /**
   * Generate HTML documentation
   */
  generateHtml(definition: IntegrationDefinition): string {
    const markdown = this.generateMarkdown(definition);
    const { marked } = require('marked');

    const html = `
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>${definition.info.title}</title>
  <style>
    body { font-family: Arial, sans-serif; max-width: 1200px; margin: 0 auto; padding: 20px; }
    h1 { color: #2c3e50; border-bottom: 2px solid #3498db; }
    h2 { color: #34495e; margin-top: 30px; }
    code { background: #f4f4f4; padding: 2px 6px; border-radius: 3px; }
    pre { background: #f4f4f4; padding: 15px; border-radius: 5px; overflow-x: auto; }
  </style>
</head>
<body>
  ${marked(markdown)}
</body>
</html>
    `;

    return html;
  }
}
```

#### 1.4 CLI Tool (`packages/cli`)

```typescript
// packages/cli/src/index.ts

import { Command } from 'commander';
import { IntegrationDefinitionParser, ValidationEngine, CodeGenerator, DocumentationGenerator } from '@bac4/core';
import fs from 'fs/promises';

const program = new Command();

program
  .name('bac4')
  .description('BAC4 Integration Modeller CLI')
  .version('1.0.0');

program
  .command('validate <file>')
  .description('Validate an integration definition')
  .action(async (file: string) => {
    const json = await fs.readFile(file, 'utf-8');
    const parser = new IntegrationDefinitionParser();
    const validator = new ValidationEngine();

    try {
      const definition = parser.parse(json);
      const result = await validator.validate(definition);

      if (result.valid) {
        console.log('✓ Validation passed');
      } else {
        console.error('✗ Validation failed:');
        for (const error of result.errors) {
          console.error(`  - ${error.path}: ${error.message}`);
        }
        process.exit(1);
      }

      if (result.warnings.length > 0) {
        console.warn('⚠ Warnings:');
        for (const warning of result.warnings) {
          console.warn(`  - ${warning.path}: ${warning.message}`);
        }
      }
    } catch (error) {
      console.error('Error:', error.message);
      process.exit(1);
    }
  });

program
  .command('generate <file>')
  .description('Generate code from integration definition')
  .option('-t, --target <target>', 'Target platform (camel, spring-integration, nodejs)', 'camel')
  .option('-o, --output <dir>', 'Output directory', './generated')
  .action(async (file: string, options: any) => {
    const json = await fs.readFile(file, 'utf-8');
    const parser = new IntegrationDefinitionParser();
    const generator = new CodeGenerator();

    try {
      const definition = parser.parse(json);
      const artifacts = await generator.generate(definition, {
        platform: options.target,
        version: '1.0',
        language: 'java'
      });

      // Write generated files
      for (const file of artifacts.files) {
        const filePath = `${options.output}/${file.path}`;
        await fs.mkdir(filePath.substring(0, filePath.lastIndexOf('/')), { recursive: true });
        await fs.writeFile(filePath, file.content);
      }

      console.log(`✓ Generated ${artifacts.files.length} files to ${options.output}`);
    } catch (error) {
      console.error('Error:', error.message);
      process.exit(1);
    }
  });

program
  .command('docs <file>')
  .description('Generate documentation from integration definition')
  .option('-f, --format <format>', 'Output format (markdown, html, pdf)', 'markdown')
  .option('-o, --output <file>', 'Output file', './documentation')
  .action(async (file: string, options: any) => {
    const json = await fs.readFile(file, 'utf-8');
    const parser = new IntegrationDefinitionParser();
    const docGen = new DocumentationGenerator();

    try {
      const definition = parser.parse(json);

      let content: string;
      let extension: string;

      switch (options.format) {
        case 'html':
          content = docGen.generateHtml(definition);
          extension = 'html';
          break;
        case 'markdown':
        default:
          content = docGen.generateMarkdown(definition);
          extension = 'md';
          break;
      }

      const outputFile = `${options.output}.${extension}`;
      await fs.writeFile(outputFile, content);

      console.log(`✓ Documentation generated: ${outputFile}`);
    } catch (error) {
      console.error('Error:', error.message);
      process.exit(1);
    }
  });

program.parse();
```

#### 1.5 Testing

**Unit Tests**:
```typescript
// packages/core/tests/parser.test.ts

import { IntegrationDefinitionParser } from '../src/parser/integration-definition-parser';

describe('IntegrationDefinitionParser', () => {
  let parser: IntegrationDefinitionParser;

  beforeEach(() => {
    parser = new IntegrationDefinitionParser();
  });

  it('should parse valid integration definition', () => {
    const json = `{
      "version": "1.0.0",
      "info": {
        "title": "Test Integration",
        "version": "1.0.0"
      },
      "systems": {},
      "integrations": []
    }`;

    const definition = parser.parse(json);
    expect(definition.info.title).toBe('Test Integration');
  });

  it('should throw error for invalid definition', () => {
    const json = `{ "invalid": true }`;

    expect(() => parser.parse(json)).toThrow();
  });

  it('should validate required fields', () => {
    const json = `{
      "version": "1.0.0",
      "info": {
        "title": "Test"
      }
    }`;

    expect(() => parser.parse(json)).toThrow();
  });
});
```

**Integration Tests**:
```typescript
// packages/core/tests/integration/generate.test.ts

import { IntegrationDefinitionParser, CodeGenerator } from '../../src';
import fs from 'fs/promises';

describe('Code Generation Integration', () => {
  it('should generate Apache Camel code from REST API integration', async () => {
    const json = await fs.readFile('./examples/rest-api-integration.json', 'utf-8');
    const parser = new IntegrationDefinitionParser();
    const generator = new CodeGenerator();

    const definition = parser.parse(json);
    const artifacts = await generator.generate(definition, {
      platform: 'camel',
      version: '4.0',
      language: 'java'
    });

    expect(artifacts.files.length).toBeGreaterThan(0);
    expect(artifacts.files[0].content).toContain('RouteBuilder');
  });
});
```

---

### Phase 2: Web UI & Backend API

#### 2.1 Backend API (`packages/api`)

**Framework**: NestJS with TypeScript

**Project structure:**
```
packages/api/
├── src/
│   ├── modules/
│   │   ├── integrations/
│   │   ├── systems/
│   │   ├── catalog/
│   │   ├── generation/
│   │   └── auth/
│   ├── common/
│   ├── config/
│   └── main.ts
├── test/
└── package.json
```

**Key Modules:**

1. **Integrations Module**:
```typescript
// src/modules/integrations/integrations.controller.ts

import { Controller, Get, Post, Put, Delete, Body, Param } from '@nestjs/common';
import { IntegrationsService } from './integrations.service';
import { CreateIntegrationDto, UpdateIntegrationDto } from './dto';

@Controller('api/v1/integrations')
export class IntegrationsController {
  constructor(private readonly integrationsService: IntegrationsService) {}

  @Post()
  async create(@Body() dto: CreateIntegrationDto) {
    return this.integrationsService.create(dto);
  }

  @Get()
  async findAll() {
    return this.integrationsService.findAll();
  }

  @Get(':id')
  async findOne(@Param('id') id: string) {
    return this.integrationsService.findOne(id);
  }

  @Put(':id')
  async update(@Param('id') id: string, @Body() dto: UpdateIntegrationDto) {
    return this.integrationsService.update(id, dto);
  }

  @Delete(':id')
  async remove(@Param('id') id: string) {
    return this.integrationsService.remove(id);
  }

  @Post(':id/validate')
  async validate(@Param('id') id: string) {
    return this.integrationsService.validate(id);
  }

  @Post(':id/generate')
  async generate(@Param('id') id: string, @Body() options: any) {
    return this.integrationsService.generate(id, options);
  }
}
```

2. **Database Setup** (Prisma):
```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Integration {
  id              String   @id @default(uuid())
  title           String
  version         String
  pattern         String
  definitionJson  Json
  tags            String[]
  owner           String?
  status          String   @default("draft")
  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt
  createdBy       String?
  updatedBy       String?

  versions        IntegrationVersion[]

  @@index([title])
  @@index([pattern])
  @@index([tags])
  @@index([owner])
}

model IntegrationVersion {
  id              String   @id @default(uuid())
  integrationId   String
  version         String
  definitionJson  Json
  changelog       String?
  createdAt       DateTime @default(now())
  createdBy       String?

  integration     Integration @relation(fields: [integrationId], references: [id], onDelete: Cascade)

  @@index([integrationId])
}

model System {
  id          String   @id @default(uuid())
  name        String   @unique
  type        String
  description String?
  owner       String?
  environment String?
  metadata    Json?
  createdAt   DateTime @default(now())

  @@index([name])
  @@index([type])
}
```

#### 2.2 Frontend Web UI (`packages/web-ui`)

**Framework**: React 18 + TypeScript + Material-UI

**Project structure:**
```
packages/web-ui/
├── src/
│   ├── components/
│   │   ├── Designer/
│   │   ├── Editor/
│   │   ├── Catalog/
│   │   └── Documentation/
│   ├── pages/
│   ├── services/
│   ├── hooks/
│   ├── store/
│   ├── types/
│   └── App.tsx
├── public/
└── package.json
```

**Key Components:**

1. **Visual Designer Component**:
```typescript
// src/components/Designer/IntegrationDesigner.tsx

import React, { useState, useCallback } from 'react';
import ReactFlow, {
  Node,
  Edge,
  Controls,
  Background,
  applyNodeChanges,
  applyEdgeChanges,
} from 'reactflow';
import 'reactflow/dist/style.css';

export const IntegrationDesigner: React.FC = () => {
  const [nodes, setNodes] = useState<Node[]>([]);
  const [edges, setEdges] = useState<Edge[]>([]);

  const onNodesChange = useCallback(
    (changes) => setNodes((nds) => applyNodeChanges(changes, nds)),
    []
  );

  const onEdgesChange = useCallback(
    (changes) => setEdges((eds) => applyEdgeChanges(changes, eds)),
    []
  );

  const onConnect = useCallback(
    (params) => setEdges((eds) => [...eds, params]),
    []
  );

  return (
    <div style={{ height: '100vh' }}>
      <ReactFlow
        nodes={nodes}
        edges={edges}
        onNodesChange={onNodesChange}
        onEdgesChange={onEdgesChange}
        onConnect={onConnect}
        fitView
      >
        <Controls />
        <Background />
      </ReactFlow>
    </div>
  );
};
```

2. **JSON Editor Component**:
```typescript
// src/components/Editor/JsonEditor.tsx

import React from 'react';
import Editor from '@monaco-editor/react';

interface JsonEditorProps {
  value: string;
  onChange: (value: string) => void;
  schema?: any;
}

export const JsonEditor: React.FC<JsonEditorProps> = ({ value, onChange, schema }) => {
  return (
    <Editor
      height="100vh"
      language="json"
      value={value}
      onChange={(value) => onChange(value || '')}
      theme="vs-dark"
      options={{
        minimap: { enabled: false },
        scrollBeyondLastLine: false,
        automaticLayout: true,
      }}
    />
  );
};
```

---

### Phase 3: Extended Patterns & Templates

#### 3.1 Code Generation Templates

Create Handlebars templates for each platform:

**Apache Camel Route Builder Template**:
```handlebars
{{!-- templates/camel/java/RouteBuilder.java.hbs --}}
package com.example.routes;

import org.apache.camel.builder.RouteBuilder;
import org.springframework.stereotype.Component;

@Component
public class {{pascalCase id}}Route extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        {{#ifEquals pattern "rest-api"}}
        from("{{source.protocol}}://{{source.connection.url}}")
            {{#if transformation}}
            .transform()
                .jsonpath("{{transformation.mapping}}")
            {{/if}}
            .to("{{target.protocol}}://{{target.connection.url}}");
        {{/ifEquals}}

        {{#ifEquals pattern "event-stream"}}
        from("kafka:{{source.connection.topic}}?brokers={{source.connection.host}}:{{source.connection.port}}")
            .log("Received message: ${body}")
            {{#if transformation}}
            .transform()
                .jsonpath("{{transformation.mapping}}")
            {{/if}}
            .to("kafka:{{target.connection.topic}}?brokers={{target.connection.host}}:{{target.connection.port}}");
        {{/ifEquals}}

        {{#if errorHandling.deadLetterQueue.enabled}}
        onException(Exception.class)
            .maximumRedeliveries({{errorHandling.retry.maxAttempts}})
            .to("{{errorHandling.deadLetterQueue.endpoint.protocol}}://{{errorHandling.deadLetterQueue.endpoint.connection.topic}}");
        {{/if}}
    }
}
```

**Spring Integration DSL Template**:
```handlebars
{{!-- templates/spring-integration/java/IntegrationConfig.java.hbs --}}
package com.example.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.integration.dsl.IntegrationFlow;
import org.springframework.integration.dsl.IntegrationFlows;

@Configuration
public class {{pascalCase id}}IntegrationConfig {

    @Bean
    public IntegrationFlow {{camelCase id}}Flow() {
        return IntegrationFlows
            .from("{{source.protocol}}.inbound.{{source.connection.channel}}")
            {{#if transformation}}
            .transform(transformer())
            {{/if}}
            .channel("{{target.protocol}}.outbound.{{target.connection.channel}}")
            .get();
    }
}
```

**Node.js Express Template**:
```handlebars
{{!-- templates/nodejs/express/route.ts.hbs --}}
import { Router } from 'express';
import axios from 'axios';

const router = Router();

router.{{toLowerCase config.method}}('{{config.path}}', async (req, res) => {
  try {
    {{#if transformation}}
    const transformedData = transformData(req.body);
    {{else}}
    const transformedData = req.body;
    {{/if}}

    const response = await axios.{{toLowerCase config.method}}(
      '{{target.connection.url}}{{config.path}}',
      transformedData,
      {
        headers: {{json config.headers indent=2}},
        timeout: {{config.timeout}}
      }
    );

    res.status(response.status).json(response.data);
  } catch (error) {
    {{#if errorHandling.retry}}
    // Retry logic
    {{/if}}
    res.status(500).json({ error: error.message });
  }
});

{{#if transformation}}
function transformData(data: any): any {
  // Transformation logic
  return {
    {{#each transformation.mapping}}
    {{@key}}: data.{{this}},
    {{/each}}
  };
}
{{/if}}

export default router;
```

---

### Phase 4: Plugin System

#### 4.1 Plugin Architecture

**Plugin Base Class**:
```typescript
// packages/core/src/plugins/plugin-base.ts

export interface PluginMetadata {
  name: string;
  version: string;
  type: PluginType;
  description: string;
  author: string;
}

export enum PluginType {
  PATTERN = 'pattern',
  GENERATOR = 'generator',
  VALIDATOR = 'validator',
  TRANSFORMATION = 'transformation',
  CONNECTOR = 'connector'
}

export interface PluginContext {
  config: any;
  logger: Logger;
  services: {
    validation: ValidationEngine;
    generation: CodeGenerator;
    transformation: TransformationEngine;
  };
}

export abstract class PluginBase {
  abstract metadata: PluginMetadata;

  abstract initialize(context: PluginContext): Promise<void>;
  abstract execute(input: any): Promise<any>;

  async cleanup(): Promise<void> {
    // Optional cleanup
  }
}
```

**Plugin Loader**:
```typescript
// packages/core/src/plugins/plugin-loader.ts

export class PluginLoader {
  private plugins: Map<string, PluginBase> = new Map();

  async loadPlugin(pluginPath: string): Promise<void> {
    const manifest = await this.readPluginManifest(pluginPath);

    // Validate plugin
    this.validatePlugin(manifest);

    // Load plugin module
    const PluginClass = require(path.join(pluginPath, manifest.main));
    const plugin = new PluginClass();

    // Initialize plugin
    await plugin.initialize(this.createPluginContext());

    // Register plugin
    this.plugins.set(manifest.name, plugin);
  }

  async loadPluginsFromDirectory(directory: string): Promise<void> {
    const pluginDirs = await fs.readdir(directory);

    for (const dir of pluginDirs) {
      const pluginPath = path.join(directory, dir);
      await this.loadPlugin(pluginPath);
    }
  }

  getPlugin(name: string): PluginBase | undefined {
    return this.plugins.get(name);
  }

  listPlugins(): PluginMetadata[] {
    return Array.from(this.plugins.values()).map(p => p.metadata);
  }
}
```

**Example Plugin** (Custom Pattern):
```typescript
// plugins/custom-graphql-pattern/index.ts

import { PluginBase, PluginMetadata, PluginContext, PluginType } from '@bac4/core';

export default class GraphQLPatternPlugin extends PluginBase {
  metadata: PluginMetadata = {
    name: 'graphql-pattern',
    version: '1.0.0',
    type: PluginType.PATTERN,
    description: 'Adds support for GraphQL integration pattern',
    author: 'BAC4 Team'
  };

  async initialize(context: PluginContext): Promise<void> {
    // Register custom JSON Schema for GraphQL pattern
    context.services.validation.registerSchema('graphql-config', {
      type: 'object',
      properties: {
        endpoint: { type: 'string' },
        query: { type: 'string' },
        variables: { type: 'object' }
      }
    });

    // Register code generator for GraphQL
    context.services.generation.registerGenerator('graphql', async (definition) => {
      // Generate GraphQL client code
      return {
        files: [/* ... */],
        dependencies: [{ name: 'graphql', version: '16.0.0' }]
      };
    });
  }

  async execute(input: any): Promise<any> {
    // Custom execution logic
    return input;
  }
}
```

---

### Phase 5: Deployment & DevOps

#### 5.1 Docker Configuration

**Multi-stage Dockerfile** (API):
```dockerfile
# packages/api/Dockerfile

FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package.json ./

EXPOSE 8080
CMD ["node", "dist/main.js"]
```

**Docker Compose**:
```yaml
# docker-compose.yml

version: '3.8'

services:
  web:
    build:
      context: ./packages/web-ui
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://api:8080
    depends_on:
      - api

  api:
    build:
      context: ./packages/api
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgresql://bac4:password@db:5432/bac4
      - REDIS_URL=redis://redis:6379
      - JWT_SECRET=${JWT_SECRET}
    depends_on:
      - db
      - redis

  db:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=bac4
      - POSTGRES_USER=bac4
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

#### 5.2 CI/CD Pipeline

**GitHub Actions**:
```yaml
# .github/workflows/ci.yml

name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm run test

      - name: Build
        run: npm run build

  build-and-push:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - uses: actions/checkout@v3

      - name: Build and push Docker images
        run: |
          docker build -t bac4/api:latest ./packages/api
          docker build -t bac4/web-ui:latest ./packages/web-ui
          docker push bac4/api:latest
          docker push bac4/web-ui:latest

  deploy:
    needs: build-and-push
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: |
          # Deployment script
          echo "Deploying to production..."
```

---

## Testing Strategy

### Unit Tests
- Test all core modules (parser, validator, generator)
- Test individual React components
- Test API endpoints
- Aim for 80%+ code coverage

### Integration Tests
- Test end-to-end code generation
- Test API integration with database
- Test plugin system

### E2E Tests
- Test complete user workflows (create → validate → generate)
- Use Playwright or Cypress

---

## Documentation Requirements

1. **README.md**: Project overview, setup instructions, usage
2. **CONTRIBUTING.md**: Contribution guidelines
3. **API.md**: REST API documentation
4. **PLUGINS.md**: Plugin development guide
5. **EXAMPLES.md**: Example integration definitions
6. **ARCHITECTURE.md**: Architecture documentation

---

## Success Criteria

**MVP (Phase 1)**:
- ✅ JSON Schema defined and validated
- ✅ Parser and validator working
- ✅ Basic code generator (Apache Camel)
- ✅ CLI tool functional
- ✅ Unit tests passing (80% coverage)

**Full Product**:
- ✅ All integration patterns supported
- ✅ Web UI with visual designer
- ✅ Multiple code generators
- ✅ Plugin system functional
- ✅ Documentation complete
- ✅ E2E tests passing
- ✅ Docker deployment ready

---

## Next Steps After Build

1. **Beta Testing**: Gather feedback from architects
2. **Documentation**: Create video tutorials and guides
3. **Community**: Open-source on GitHub, build community
4. **Marketplace**: Create plugin marketplace
5. **Enterprise Features**: Add RBAC, multi-tenancy, monitoring

---

## Support & Resources

- JSON Schema: https://json-schema.org/
- AsyncAPI: https://www.asyncapi.com/
- OpenAPI: https://swagger.io/specification/
- Apache Camel: https://camel.apache.org/
- Handlebars: https://handlebarsjs.com/
- React Flow: https://reactflow.dev/
- NestJS: https://nestjs.com/

---

**This build prompt provides a complete guide for implementing the BAC4 Integration Modeller. Follow the phases sequentially and adapt as needed based on your team's preferences and requirements.**
