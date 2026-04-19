# Amazon Device Farm (amazon-device-farm)
AWS Device Farm is an application testing service that lets you improve the quality of your web and mobile apps by testing them across an extensive range of desktop browsers and real mobile devices without having to provision and manage any testing infrastructure.

**URL:** [Visit Amazon Device Farm](https://aws.amazon.com/device-farm/)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - Application Testing, AWS, Device Testing, Mobile Testing, Quality Assurance

## Timestamps

- **Created:** 2026-03-16
- **Modified:** 2026-04-19

## APIs

### AWS Device Farm API
The AWS Device Farm API enables programmatic access to create and manage test runs, device pools, projects, and jobs for testing mobile and web applications across real devices and browsers. Supports 77 operations covering projects, runs, devices, uploads, remote access sessions, Selenium test grid, network profiles, instance profiles, VPC endpoint configurations, and resource tagging.

**Human URL:** [https://aws.amazon.com/device-farm/](https://aws.amazon.com/device-farm/)

#### Tags:

 - Application Testing, Device Management, Mobile Testing, Quality Assurance

#### Properties

- [Documentation](https://docs.aws.amazon.com/devicefarm/latest/APIReference/Welcome.html)
- [OpenAPI](openapi/amazon-device-farm-openapi.yaml)
- [GettingStarted](https://aws.amazon.com/device-farm/getting-started/)
- [Pricing](https://aws.amazon.com/device-farm/pricing/)
- [FAQ](https://aws.amazon.com/device-farm/faqs/)
- [JSONSchema - Project](json-schema/amazon-device-farm-project-schema.json)
- [JSONSchema - Run](json-schema/amazon-device-farm-run-schema.json)
- [JSONSchema - Device](json-schema/amazon-device-farm-device-schema.json)
- [JSONSchema - Upload](json-schema/amazon-device-farm-upload-schema.json)
- [JSONSchema - Device Pool](json-schema/amazon-device-farm-device-pool-schema.json)
- [JSONStructure - Project](json-structure/amazon-device-farm-project-structure.json)
- [JSONStructure - Run](json-structure/amazon-device-farm-run-structure.json)
- [JSONStructure - Device](json-structure/amazon-device-farm-device-structure.json)
- [JSON-LD](json-ld/amazon-device-farm-context.jsonld)
- [Example - Project](examples/amazon-device-farm-project-example.json)
- [Example - Run](examples/amazon-device-farm-run-example.json)
- [Example - Device](examples/amazon-device-farm-device-example.json)

## Common Properties

- [Portal](https://aws.amazon.com/device-farm/)
- [Website](https://aws.amazon.com/device-farm/)
- [Documentation](https://docs.aws.amazon.com/devicefarm/)
- [TermsOfService](https://aws.amazon.com/service-terms/)
- [PrivacyPolicy](https://aws.amazon.com/privacy/)
- [Support](https://aws.amazon.com/premiumsupport/)
- [Blog](https://aws.amazon.com/blogs/mobile/category/mobile-services/aws-device-farm/)
- [GitHubOrganization](https://github.com/aws)
- [Console](https://console.aws.amazon.com/devicefarm/)
- [SignUp](https://portal.aws.amazon.com/billing/signup)
- [Login](https://signin.aws.amazon.com/)
- [StatusPage](https://health.aws.amazon.com/health/status)
- [Contact](https://aws.amazon.com/contact-us/)
- [SpectralRules](rules/amazon-device-farm-spectral-rules.yml)
- [Vocabulary](vocabulary/amazon-device-farm-vocabulary.yaml)
- [NaftikoCapability](capabilities/mobile-browser-testing.yaml)

## Features

| Name | Description |
|------|-------------|
| Real Device Testing | Test mobile apps on thousands of real physical Android and iOS devices without managing any device infrastructure. |
| Desktop Browser Testing | Test web applications on desktop browsers using Selenium RemoteWebDriver through Device Farm's test grid. |
| Automated Test Frameworks | Supports popular test frameworks including Appium, XCTest, Espresso, Calabash, and built-in fuzz testing. |
| Remote Access Sessions | Interactively access real devices via remote desktop to manually test and debug your app. |
| Network Condition Simulation | Simulate different network conditions including bandwidth, latency, and packet loss using network profiles. |
| Private Device Fleet | Provision and manage private dedicated devices for exclusive use in testing your applications. |
| VPC Integration | Test apps hosted in private VPCs using VPC endpoint configurations without exposing them publicly. |
| Parallel Test Execution | Run tests in parallel across multiple devices simultaneously to reduce overall testing time. |

## Use Cases

| Name | Description |
|------|-------------|
| Mobile App Quality Assurance | Validate mobile app functionality, performance, and compatibility across a wide range of real Android and iOS devices. |
| Cross-Device Compatibility Testing | Ensure your app behaves correctly on different device manufacturers, screen sizes, OS versions, and hardware configurations. |
| Web Application Browser Testing | Run Selenium-based browser tests against your web application across multiple desktop browser environments. |
| CI/CD Test Integration | Integrate device testing into your continuous integration and deployment pipelines for automated quality gates. |
| App Performance Benchmarking | Measure app performance metrics including CPU usage, memory consumption, and network activity across different devices. |

## Integrations

| Name | Description |
|------|-------------|
| AWS CodePipeline | Integrate Device Farm tests as a stage in your CodePipeline CI/CD pipelines for automated testing. |
| AWS CodeBuild | Trigger Device Farm test runs from CodeBuild projects as part of build and deploy workflows. |
| Jenkins | Run Device Farm tests from Jenkins CI using the AWS Device Farm plugin for Jenkins. |
| Appium | Supports Appium test scripts for both Android and iOS cross-platform mobile testing. |
| Selenium | Integrates with Selenium RemoteWebDriver for automated desktop browser testing. |
| GitHub Actions | Trigger Device Farm test runs using the AWS Device Farm GitHub Action in your workflows. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [AWS Device Farm OpenAPI](openapi/amazon-device-farm-openapi.yaml)

### JSON Schema

- [Project Schema](json-schema/amazon-device-farm-project-schema.json)
- [Run Schema](json-schema/amazon-device-farm-run-schema.json)
- [Job Schema](json-schema/amazon-device-farm-job-schema.json)
- [Device Schema](json-schema/amazon-device-farm-device-schema.json)
- [Device Pool Schema](json-schema/amazon-device-farm-device-pool-schema.json)
- [Upload Schema](json-schema/amazon-device-farm-upload-schema.json)
- [Artifact Schema](json-schema/amazon-device-farm-artifact-schema.json)
- [Remote Access Session Schema](json-schema/amazon-device-farm-remote-access-session-schema.json)
- [Test Grid Project Schema](json-schema/amazon-device-farm-test-grid-project-schema.json)
- [Network Profile Schema](json-schema/amazon-device-farm-network-profile-schema.json)

### JSON Structure

- [Project Structure](json-structure/amazon-device-farm-project-structure.json)
- [Run Structure](json-structure/amazon-device-farm-run-structure.json)
- [Device Structure](json-structure/amazon-device-farm-device-structure.json)
- [Upload Structure](json-structure/amazon-device-farm-upload-structure.json)
- [Device Pool Structure](json-structure/amazon-device-farm-device-pool-structure.json)

### JSON-LD

- [Amazon Device Farm Context](json-ld/amazon-device-farm-context.jsonld)

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [AWS Device Farm API](capabilities/shared/device-farm-api.yaml) — 22 operations for project management, test scheduling, device catalog, upload handling, and remote access sessions

### Workflow Capabilities

| Workflow | APIs Combined | Tools | Persona |
|----------|--------------|-------|---------|
| [Mobile and Browser Testing](capabilities/mobile-browser-testing.yaml) | device-farm | 18 | QA Engineer, Mobile Developer |

## Vocabulary

- [Amazon Device Farm Vocabulary](vocabulary/amazon-device-farm-vocabulary.yaml) — Unified taxonomy mapping 17 resources, 11 actions, 1 workflow, and 2 personas across operational (OpenAPI) and capability (Naftiko) dimensions

## Rules

- [Amazon Device Farm Spectral Rules](rules/amazon-device-farm-spectral-rules.yml) — 25 rules across 9 categories enforcing AWS Device Farm API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
