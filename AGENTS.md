# WildFly Maven Plugin — Agent Instructions

The WildFly Maven Plugin (`org.wildfly.plugins:wildfly-maven-plugin`) is the primary developer-facing build tool for the WildFly ecosystem. It provides Maven goals for provisioning servers (via Galleon), packaging applications, building container images, deploying to running servers, executing CLI commands, and running in dev-mode with hot-reload. It integrates WildFly Glow for automatic provisioning configuration discovery.

## Navigating This Repository

1. Read `llms.txt` in the repository root for a full index of modules, goals, documentation, and key source entry points.
2. The main plugin code is in `plugin/src/main/java/org/wildfly/plugin/` — organized by concern:
   - `provision/` — Galleon provisioning and packaging goals (`PackageServerMojo`, `ProvisionServerMojo`, `ApplicationImageMojo`, `GlowConfig`)
   - `dev/` — Dev-mode goal with file watching (`DevMojo`, `WatchContext`)
   - `server/` — Server lifecycle goals (`RunMojo`, `StartMojo`, `ShutdownMojo`)
   - `deployment/` — Deploy/redeploy/undeploy goals
   - `cli/` — CLI command execution
   - `common/` — Shared utilities and management client connection
3. Documentation is in `plugin/src/site/markdown/` as Velocity template `.md.vm` files — published to https://docs.wildfly.org/wildfly-maven-plugin
4. The `core/` module provides a small shared API for deployment operations.

## Key Concepts

- **Galleon Provisioning**: The plugin uses Galleon to provision trimmed WildFly servers. Users configure feature packs and layers in their POM, or use `<discover-provisioning-info/>` to delegate to WildFly Glow.
- **WildFly Glow Integration**: The `GlowConfig` class wraps Glow's `ScanArguments` API. When `<discover-provisioning-info/>` is present, the plugin scans deployment artifacts to auto-discover required feature packs and layers. The Glow library (`org.wildfly.glow`) is a compile-time dependency of this plugin.
- **Bootable JAR**: The `package` goal can produce an executable fat JAR containing the server and application.
- **Container Images**: The `image` goal builds Docker/Podman images for cloud deployment.
- **Dev Mode**: The `dev` goal watches source directories and auto-redeploys on changes.
- **Channels**: WildFly Channels (YAML-based version manifests) can be used for reproducible provisioning.
- **CLI Scripting**: Packaging scripts execute CLI commands in an embedded server to fine-tune configuration before the server is finalized.

## Building

```
mvn clean install          # JDK 17+
```

## Ecosystem Context & Cross-Repo Routing

The WildFly Maven Plugin is part of the WildFly ecosystem. When working on a task, determine whether it is local to this repository or requires navigating to another repository.

- **Local Tasks:** For Maven goal behavior, provisioning configuration, Bootable JAR packaging, container image building, dev-mode, deployment operations, CLI execution, channel support, and plugin POM configuration, consult the local [WildFly Maven Plugin Documentation Index](https://raw.githubusercontent.com/kabir/wildfly-maven-plugin/ai-index/llms.txt).

- **Cross-Repository Tasks:** For changes involving upstream or downstream components, consult the [WildFly Central AI Hub](https://raw.githubusercontent.com/kabir/wildfly-ai-context/main/llms.txt) and look up the target project:
    - *Modifying how deployment scanning identifies required layers, changing add-on behavior, adjusting the scanning engine or rule matching system* → Navigate to **WildFly Glow** (upstream library dependency).
    - *Modifying the management model, controller operations, CLI command syntax, deployment scanner SPI, or domain mode behavior* → Navigate to **WildFly Core**.
    - *Modifying Jakarta EE subsystem behavior, Galleon feature pack definitions, Elytron security, clustering internals, or distribution packaging* → Navigate to **WildFly**.
    - *Modifying Galleon layer annotations, layer-spec.xml rule definitions, or the feature-pack compatibility registry* → Navigate to **WildFly Galleon Feature Packs** (external).
