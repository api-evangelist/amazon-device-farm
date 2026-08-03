# Amazon Device Farm (amazon-device-farm)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
