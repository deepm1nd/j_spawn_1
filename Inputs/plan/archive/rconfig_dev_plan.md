# Rconfig: gRiTOS Configuration System Development Plan

## 1. Introduction & Motivation

The gRiTOS operating system, with its strong emphasis on security, safety, and reliability, requires a robust, verifiable, and user-friendly configuration system. Traditional configuration methods often involve manual editing of text files, complex scripting, or opaque binary formats, which can be error-prone, difficult to validate, and may not integrate well with a system designed for high assurance.

**Rconfig** (Rust Configuration for gRiTOS) is envisioned as a comprehensive configuration management system specifically tailored for gRiTOS. It aims to address the shortcomings of conventional approaches by leveraging Rust's strengths in type safety, correctness, and build-time verification, combined with a clear, schema-driven configuration format.

The motivation behind Rconfig stems from several key gRiTOS requirements:
*   **GRITOS-REQ-GEN-00001:** All components, including build and configuration tools, shall be written in Rust.
*   **GRITOS-REQ-GEN-00002:** The OS shall provide a consistent and intuitive user interface for system management and configuration.
*   **GRITOS-REQ-GEN-00003:** The OS shall support a build system that allows for reproducible builds and easy customization of system components.
*   **GRITOS-REQ-CS-00001:** The OS shall provide mechanisms for enforcing system-wide security policies. (Configuration is key to defining these policies).
*   **GRITOS-REQ-CS-00002:** The OS shall provide mechanisms for system administrators to configure security parameters.
*   **GRITOS-REQ-FS-00001:** The OS shall be designed to minimize potential hazards. (Misconfiguration is a significant hazard).

Rconfig will serve as the central nervous system for gRiTOS configuration, impacting everything from kernel parameters and boot options to system service settings and security policies. Its design must therefore reflect the same rigor and quality as the OS itself.

## 2. Goals and Core Principles

### 2.1 Goals for Rconfig
The primary goals for the Rconfig system are:

1.  **Verifiability:** Enable compile-time and runtime verification of system configurations against a defined schema. This includes type checking, range validation, and dependency checking.
2.  **Safety & Security:** Prevent invalid or insecure configurations from being applied. Ensure that configuration changes are auditable and, where appropriate, require authorization.
3.  **Ease of Use:** Provide intuitive interfaces (CLI, potentially TUI/GUI stubs) for users to view and modify configurations, abstracting away underlying complexities.
4.  **Reproducibility:** Integrate seamlessly with the gRiTOS build system to ensure that system images can be built with consistent configurations.
5.  **Modularity:** Design Rconfig with distinct components (e.g., core engine, schema parser, frontends) to allow for maintainability and future extensions.
6.  **Extensibility:** Allow new gRiTOS components and applications to define their own configuration schemas and integrate them into the Rconfig framework.
7.  **Atomicity:** Ensure that configuration changes are applied atomically, preventing partial updates that could leave the system in an inconsistent state.
8.  **Traceability:** Link configuration items back to system requirements and design decisions where applicable.

### 2.2 Core Design Principles
Rconfig development will adhere to the following principles:

*   **Schema-Driven:** All configurations are defined by a clear, machine-readable schema (likely using a format like TOML or a custom DSL that compiles to an internal representation). This schema is the single source of truth for valid configuration parameters.
*   **Rust All The Way:** All custom components of Rconfig will be implemented in Rust, leveraging its safety and concurrency features.
*   **Fail-Safe by Default:** Prioritize configurations that enhance system safety and security. Default values should be chosen to be secure and stable.
*   **Build-Time Integration:** Perform as much configuration validation and processing as possible at build time to catch errors early and embed verified configurations into the system image where appropriate.
*   **Minimal Runtime Footprint:** For runtime configuration adjustments, ensure the Rconfig components running on the target have a minimal resource footprint and attack surface.
*   **Clear Separation of Concerns:** Differentiate between configuration definition, validation, storage, and application.
*   **User-Centric Abstraction:** While the backend is rigorous, user-facing tools should be as simple and intuitive as possible.

## 3. Rconfig Architecture (High-Level)

Rconfig will consist of several key architectural components:

![Rconfig Conceptual Architecture Diagram](diagrams/rconfig_architecture.png) 
*(Diagram to be created: A block diagram showing User Interfaces, Build System, Rconfig Core Engine, Schema Definitions, Configuration Storage, and Target System Components)*

### 3.1 Configuration Input Format & Schema
*   **Format:** TOML (Tom's Obvious, Minimal Language) is the primary candidate for user-facing configuration files due to its readability and simplicity.
*   **Schema Definition:** A custom Rust-based Domain Specific Language (DSL) or a set of macros will be developed to define configuration schemas. These schemas will specify:
    *   Parameter names and types (e.g., integer, string, boolean, enum, array, map).
    *   Default values.
    *   Constraints (e.g., min/max values, valid enum variants, regex for strings).
    *   Dependencies between parameters (e.g., if feature X is enabled, parameter Y must be set).
    *   Help text/documentation for each parameter.
    *   Mapping to target component settings (e.g., kernel command line, service config file path, specific struct in a driver).
*   **Schema Compilation:** Schemas will be compiled into an internal representation (e.g., Rust structs or a serialized format) that the Rconfig Core Engine can use for validation and code generation.
*   **Conceptual Module:** `rconfig_schema` (for schema definition tools and internal representation).

### 3.2 Rconfig Core Engine
This is the heart of the Rconfig system.
*   **Responsibilities:**
    *   Parsing and validating configuration files against their schemas.
    *   Generating target-specific configuration outputs (e.g., kernel command lines, flattened key-value pairs for services, C header files, Rust code snippets).
    *   Providing a library/API for other tools (build system, UI) to interact with configurations.
    *   Handling atomic updates to configuration storage (if runtime changes are supported for certain parameters).
*   **Build-Time Operations:**
    *   Validating the entire system configuration.
    *   Generating configuration artifacts to be embedded in the gRiTOS image.
*   **Runtime Operations (Limited Scope for gRiTOS):**
    *   Potentially a minimal runtime component for applying a limited set of safe, updatable configurations (e.g., network settings, debug flags) if deemed necessary and secure. This would interact with a secure configuration storage.
*   **Conceptual Module:** `rconfig_core`.

### 3.3 User Interfaces (UI)
*   **Command-Line Interface (CLI):**
    *   The primary interface for developers and system administrators.
    *   Functionality:
        *   View current configuration (merged from defaults and user overrides).
        *   Set/get specific configuration values.
        *   Validate configuration files.
        *   List available configuration parameters with descriptions and constraints.
        *   Export/import configuration snapshots.
    *   **Conceptual Module:** `rconfig_cli`.
*   **Text-based UI (TUI) / Graphical UI (GUI) Stubs:**
    *   Placeholders or very basic implementations might be considered for future development, focusing on ease of use for less technical users for specific configuration tasks. These are lower priority.
    *   **Conceptual Module:** `rconfig_tui` (low priority), `rconfig_gui` (very low priority).

### 3.4 Configuration Storage
*   **Build-Time:**
    *   Default configurations embedded in component source code (via schema-generated Rust code).
    *   System-wide configuration definitions stored in TOML files within the build system.
*   **Runtime (If Applicable):**
    *   For parameters that can be updated at runtime, a secure and reliable storage mechanism is needed. This could be:
        *   A dedicated, integrity-protected partition.
        *   Integration with a secure element or TPM for storing critical settings.
        *   A simple, checksummed file format in a read-write partition (with strict controls).
    *   The choice will depend on the security requirements for specific updatable parameters.

### 3.5 Build System Integration
*   Rconfig will be tightly integrated with the gRiTOS build system (likely based on `make` or a custom Rust build tool like `cargo xtask`).
*   **Responsibilities:**
    *   Invoking `rconfig_core` to validate configurations during the build.
    *   Passing schema definitions to `rconfig_core`.
    *   Collecting generated configuration artifacts (e.g., kernel command line, config headers) and including them in the system image.
    *   Allowing easy selection of different configuration profiles (e.g., "debug", "secure_production", "test_harness").
*   This integration is critical for achieving reproducible and verifiable system builds.

## 4. Phased Development Plan

The development of Rconfig will be approached in phases to allow for iterative progress, feedback, and adaptation. Each phase will build upon the previous one, delivering a progressively more complete and functional configuration system.

### 4.1 Phase 1: Core Engine & Schema Definition

*   **Goal:** Establish the fundamental building blocks of Rconfig: the ability to define, parse, and process a basic set of configuration options, and generate type-safe Rust code.
*   **Tasks:**
    1.  **Define Rconfig Input Format v1 (`*.rconf`):**
        *   Finalize TOML-based syntax for defining config symbols (`config`), types (`bool`, `string`, `u32`), prompts, help text, and simple default values.
        *   Support `source <path>` for including other `.rconf` files.
        *   Support `menu`/`endmenu` constructs for basic hierarchy.
    2.  **Develop `rconfig_engine::parser`:**
        *   Implement a robust TOML parser for `.rconf` files.
        *   Build an Abstract Syntax Tree (AST) or internal representation for the parsed configuration definitions.
    3.  **Implement `rconfig_engine::SymbolTable`:**
        *   Data structures to store all defined configuration symbols, their attributes (type, prompt, help, default), and their hierarchical relationships.
        *   Conceptual Structs:
            ```rust
            // In rconfig_engine/src/schema.rs
            pub struct ConfigOption {
                pub name: String,
                pub option_type: ConfigType,
                pub prompt: Option<String>,
                pub default_value: Option<ConfigValue>,
                pub help_text: Option<String>,
                // pub dependencies: Option<DependencyExpr>, // For later phase
                // pub visibility: bool, // Calculated
                // pub current_value: Option<ConfigValue>, // For later phase
            }

            pub enum ConfigType {
                Bool,
                String,
                U32,
                // Hex,
                // Enum(Vec<String>),
            }

            pub enum ConfigValue {
                Bool(bool),
                String(String),
                U32(u32),
            }
            // ... and other related structs for menus, choices etc.
            ```
    4.  **Implement Basic `rconfig_engine::OutputGenerator`:**
        *   Functionality to traverse the `SymbolTable`.
        *   Generate a `config.rs` file containing `pub const CONFIG_SYMBOL_NAME: type = value;` for each defined symbol, using its default value.
        *   Ensure generated Rust code is correctly formatted and usable.
    5.  **Basic CLI (`rconfig` binary - Phase 1):**
        *   A command: `rconfig generate --input-root <path_to_main_rconf> --output-dir <path>`
        *   This command will parse all specified `.rconf` files starting from the root, process them using default values only (no dependency resolution yet), and generate the `config.rs`.
    6.  **Initial Build System Integration:**
        *   Create a `build.rs` script in a test gRiTOS kernel (or a mock crate) that invokes the `rconfig generate` CLI command.
        *   Verify that `config.rs` is generated and can be included and used by the test kernel.
*   **Deliverables:**
    *   `rconfig_engine` crate with parsing, basic symbol table, and Rust code generation (defaults only).
    *   `rconfig` CLI tool with the `generate` command.
    *   Documentation for the `.rconf` v1 syntax.
    *   Example `.rconf` files and a demonstration of `config.rs` generation.
    *   Report on initial build system integration.

### 4.2 Phase 2: Dependency Management & User Configuration

*   **Goal:** Implement dependency handling (`depends on`) and allow user overrides of default values via a configuration file.
*   **Tasks:**
    1.  **Enhance `*.rconf` Schema:** Add `depends on = "EXPR"` syntax. Define expression grammar (symbols, `&&`, `||`, `!`, grouping `()`).
    2.  **Implement `rconfig_engine::DependencyResolver`:**
        *   Parse dependency strings into an internal expression tree.
        *   Evaluate option visibility based on the state of their dependencies.
        *   Implement logic for tristate-like behavior if gRiTOS needs it (e.g., an option can be `y`, `n`, or `m` if its dependencies allow it, though the primary output to Rust will likely be `bool` or `Option<T>`). For gRiTOS, this might translate to `bool` for features and `Option<Type>` for parameters that are only set if a feature is enabled.
    3.  **User Configuration File (`.gritos.config`):**
        *   Define format (e.g., simple `KEY=VALUE` text file, or TOML).
        *   Rconfig engine learns to load this file to override default values.
        *   Rconfig engine learns to save the current (possibly modified by user) configuration back to this file.
    4.  **Update `rconfig_engine::SymbolTable` and Value Calculation:**
        *   `ConfigOption` struct to store `current_value` and calculated `visibility`.
        *   Logic to determine an option's value: user value (from `.gritos.config`) if present and visible, else default value if visible, else not set (or a base 'n' value).
    5.  **Enhance CLI:**
        *   `rconfig load .gritos.config`
        *   `rconfig save .gritos.config`
        *   `rconfig generate` now considers user values and dependencies.
        *   `rconfig oldconfig`: If `.gritos.config` is missing options present in `.rconf` files, prompt user for them.
    6.  **Basic `selects` / `implies` (Optional, if deemed safe):**
        *   Implement initial support for forward-selecting/implying symbols if a robust and non-circular mechanism can be designed. Prioritize safety over full Kconfig `select` behavior.
*   **Deliverables:**
    *   Updated `rconfig_engine` with dependency resolution and user configuration loading/saving.
    *   Enhanced `rconfig` CLI with load/save/oldconfig.
    *   Documentation for dependency syntax.
    *   Test cases for various dependency scenarios.

### 4.3 Phase 3: Interactive TUI & Advanced Features

*   **Goal:** Provide a user-friendly interactive configuration experience and add more advanced configuration capabilities.
*   **Tasks:**
    1.  **Develop TUI (`rconfig menuconfig`):**
        *   Use a Rust TUI crate (e.g., `cursive`, `ratatui`).
        *   Display hierarchical menus based on `.rconf` structure.
        *   Show prompts, help text, current values, and types.
        *   Allow modification of values (bool toggles, string/numeric input).
        *   Reflect dependency constraints in real-time (e.g., grey out options whose dependencies are not met).
        *   Implement search functionality.
        *   Load and save user configurations (`.gritos.config`).
    2.  **Implement `choice` / `endchoice`:** Support for exclusive selection groups.
    3.  **Refine `selects` / `implies`:** Ensure robust handling and clear user feedback on consequences.
    4.  **Support for Configuration Profiles:**
        *   Allow defining multiple base configuration profiles (e.g., `profile_asil_d.config`, `profile_cal3.config`, `profile_minimal.config`).
        *   CLI/TUI options to load a base profile and then customize it.
    5.  **Enhanced Type System:** Support for `hex`, `enum(A, B, C)` types in `.rconf` and their representation in the TUI and `config.rs`.
    6.  **Range Constraints:** Implement `range min max` for numerical types.
*   **Deliverables:**
    *   Functional TUI for Rconfig.
    *   Support for `choice`, richer types, and ranges.
    *   Mechanism for managing and applying configuration profiles.

### 4.4 Phase 4: Testing, Documentation, and Refinement

*   **Goal:** Ensure Rconfig is robust, well-documented, and ready for wider use within the gRiTOS project.
*   **Tasks:**
    1.  **Comprehensive Testing:**
        *   Unit tests for all `rconfig_engine` modules (parser, resolver, generator).
        *   Integration tests for CLI and TUI behavior with various `.rconf` structures and user inputs.
        *   Fuzz testing for the parser.
        *   Test with complex, real-world gRiTOS configuration scenarios.
    2.  **Complete Documentation:**
        *   User manual for Rconfig (CLI, TUI, `.rconf` syntax, profile management).
        *   Developer documentation for `rconfig_engine` APIs (if it's intended to be used as a library by other tools).
    3.  **Performance Optimization:** Profile Rconfig on large configuration sets and optimize parsing, dependency resolution, and code generation if necessary.
    4.  **Error Handling and Diagnostics:** Refine all error messages to be as clear and actionable as possible.
    5.  **Community Feedback & Iteration:** Gather feedback from gRiTOS developers and iterate on features and usability.
*   **Deliverables:**
    *   A well-tested and documented Rconfig tool.
    *   Performance benchmarks.
    *   Finalized version of `rconfig_dev_plan.md` (this document) if any learnings necessitate updates.

This phased approach allows for early deliverables and continuous improvement based on practical experience gained during gRiTOS development.

## 5. Addressing gRiTOS Specific Requirements

Rconfig must be more than a generic configuration tool; it needs to directly support gRiTOS's core requirements for Functional Safety (FS) and Cybersecurity (CS), including the management of configurations for different (A)SIL and CAL levels.

### 5.1 Functional Safety (FS) Support

*   **Traceable Configurations:**
    *   Rconfig will ensure that each configuration choice and its generated output (e.g., specific `CONFIG_XYZ = true` in `config.rs`) can be traced back to a defined Rconfig option and its selection state (user-set, default, or dependency-driven).
    *   The saved user configuration file (e.g., `.gritos.config`) will serve as a record of explicit choices made for a particular build.
*   **Validated Outputs & Checksums:**
    *   The generated `config.rs` can include a checksum or hash of the input configuration files and user choices that produced it. This allows the build system or runtime components to verify that the compiled configuration matches the intended source configuration.
    *   Rconfig can enforce constraints to prevent enabling combinations of features that are known to be unsafe or have not been validated for a specific safety profile.
*   **Support for Pre-defined Safety Profiles:**
    *   Rconfig will allow defining base configuration files that represent specific safety integrity levels (e.g., `asil_b_profile.rconf`, `asil_d_profile.rconf`).
    *   These profiles would pre-select a set of known-safe, validated options. Developers can then build upon these profiles, with Rconfig potentially warning or restricting changes that might violate the safety assumptions of the base profile.
    *   Example: An ASIL-D profile might disable all non-essential debugging features, enforce specific memory allocation strategies, and select only formally verified driver components.
*   **Restricted Option Sets:** For certified builds, Rconfig can operate in a mode where only a subset of configuration options (those verified for the target (A)SIL) are available or modifiable.

### 5.2 Cybersecurity (CS) Support

*   **Management of Security-Critical Options:**
    *   Security-sensitive options (e.g., enabling/disabling cryptographic modules, debug ports, default passwords, stack canaries, ASLR) will be clearly marked in the Rconfig schema, possibly with specific warnings in help text or the TUI.
*   **Prevention of Insecure Configurations:**
    *   Dependencies and constraints within Rconfig can be used to prevent users from selecting combinations of options known to be insecure (e.g., enabling a network service without enabling its required firewall protection).
*   **Auditable Configuration Trail:**
    *   The `.gritos.config` file, along with version-controlled `.rconf` schema files, provides an auditable trail of how a particular gRiTOS build was configured.
    *   Generated `config.rs` can include metadata about the Rconfig version and source files used.
*   **Support for CAL Profiles:** Similar to (A)SIL profiles, Rconfig will support defining and applying base configurations tailored for specific Cybersecurity Assurance Levels, enabling only features and defenses appropriate for that level.

### 5.3 (A)SIL / CAL Level Configuration Management

*   Rconfig will facilitate managing configurations for different (A)SIL and CAL levels as required by `GRITOS-REQ-GEN-00020`.
*   This will likely involve:
    1.  **Base Profile Files:** A set of `.rconf` or `.gritos.config` files that define the mandated settings for each specific (A)SIL/CAL level (e.g., `asil_b.config`, `cal_2.config`). These would be curated and validated.
    2.  **Layering:** Rconfig could allow layering configurations: start with a base (A)SIL profile, then overlay a CAL profile, then hardware-specific options, then application-specific features.
    3.  **Constraint Enforcement:** The Rconfig engine, when a specific (A)SIL/CAL profile is active, could strictly enforce rules associated with that profile, preventing the selection of disallowed options or combinations.
    4.  **Reporting:** Rconfig could generate a report stating which profiles are active and confirming that the current configuration complies with their constraints.

## 6. Data Structures & Code Snippets (Illustrative Summary)

This section summarizes key conceptual Rust structs for Rconfig's internal representation and its generated output. These are illustrative and subject to refinement during implementation.

### 6.1 Rconfig Engine Internal Structures (Conceptual)

```rust
// From rconfig_engine/src/schema.rs (simplified)
pub enum ConfigType {
    Bool,
    String,
    U32,
    Hex,
    Enum(Vec<String>),
    // Tristate (if gRiTOS requires this for module-like behavior)
}

pub enum ConfigValue {
    Bool(bool),
    String(String),
    U32(u32),
    Hex(u64),
    EnumSelection(String),
    // TristateValue(Tristate),
}

// Represents a defined configuration option
pub struct ConfigOptionDefinition {
    pub id: String, // e.g., "GRITOS_KERNEL_HAVE_SMP"
    pub option_type: ConfigType,
    pub prompt: Option<String>,
    pub help_text: Option<String>,
    pub default_values: Vec<ConditionalDefault>, // Defaults can depend on other configs
    pub dependencies: Vec<DependencyExpression>, // e.g., parsed "FEATURE_A && !FEATURE_B"
    pub selects: Vec<SelectExpression>,      // For reverse dependencies
    pub implies: Vec<ImplyExpression>,      // For weak reverse dependencies
    pub range: Option<(ConfigValue, ConfigValue)>, // For numerical types
    pub menu_path: Vec<String>, // Breadcrumb of menus it belongs to
}

pub struct ConditionalDefault {
    pub value: ConfigValue,
    pub condition: Option<DependencyExpression>,
}

// DependencyExpression, SelectExpression, ImplyExpression would be enums
// representing the parsed logic (Symbol, And, Or, Not, etc.)

// Represents the live state of an option
pub struct LiveConfigOption {
    pub definition: Arc<ConfigOptionDefinition>, // Reference to its schema
    pub current_value: Option<ConfigValue>,
    pub is_visible: bool,
    pub is_user_set: bool,
    // pub derived_from: ... // How its value was determined
}
```

### 6.2 Generated `config.rs` (Conceptual Output)

```rust
// Generated by Rconfig for gRiTOS -- DO NOT EDIT

// Example for boolean options
pub const CONFIG_GRITOS_KERNEL_HAVE_SMP: bool = true;
pub const CONFIG_GRITOS_DRIVERS_SERIAL_CONSOLE_ENABLE: bool = true;

// Example for string options
pub const CONFIG_GRITOS_SYSTEM_HOSTNAME: &str = "gritos-device";

// Example for numerical options
pub const CONFIG_GRITOS_KERNEL_MAX_THREADS: u32 = 1024;
pub const CONFIG_GRITOS_MEMORY_KERNEL_STACK_SIZE: usize = 0x4000; // Example hex

// Example for enum options
#[derive(Debug, PartialEq, Eq)]
pub enum KernelTimerFrequency {
    Hz100,
    Hz250,
    Hz1000,
}
pub const CONFIG_GRITOS_KERNEL_TIMER_FREQ: KernelTimerFrequency = KernelTimerFrequency::Hz100;

// Alternatively, a single static struct for all config:
pub struct GeneratedGritosConfig {
    pub have_smp: bool,
    pub serial_console_enabled: bool,
    pub hostname: &'static str,
    pub max_threads: u32,
    pub kernel_stack_size: usize,
    pub timer_freq: KernelTimerFrequency,
}

pub const GRITOS_CONFIG: GeneratedGritosConfig = GeneratedGritosConfig {
    have_smp: true,
    serial_console_enabled: true,
    hostname: "gritos-device",
    max_threads: 1024,
    kernel_stack_size: 0x4000,
    timer_freq: KernelTimerFrequency::Hz100,
    // ... all other options
};

// Metadata (optional)
pub const RCONFIG_METADATA_CONFIG_HASH: &str = "abcdef123456..."; // Hash of .gritos.config
pub const RCONFIG_METADATA_SCHEMA_HASH: &str = "fedcba654321..."; // Hash of all .rconf files
```

## 7. Future Considerations

While the phased plan above covers the core development of Rconfig, several future enhancements could further improve its capabilities and usability:

*   **Graphical User Interface (GUI):** A web-based or native GUI could complement the TUI for users who prefer graphical interaction.
*   **Language Server Protocol (LSP) Support:** An LSP server for `.rconf` files could provide real-time diagnostics, auto-completion, and help text integration within IDEs.
*   **More Sophisticated Constraint Solving:** For extremely complex dependency scenarios or to guarantee global properties of a configuration, integrating a SAT solver or other constraint programming techniques could be explored, though this significantly increases complexity.
*   **Configuration Auditing and Reporting:** Generating detailed reports on why certain options were enabled/disabled, or comparing configuration differences between profiles.
*   **Integration with Test Frameworks:** Allowing test configurations to be managed by Rconfig, or using Rconfig output to control test execution.
*   **Support for Runtime Configuration Parameters:** While primarily for compile-time, Rconfig could potentially manage a schema for runtime parameters that are loaded by the OS at boot or by services at startup, ensuring consistency between build-time and runtime settings.

These future considerations will be evaluated based on the evolving needs of the gRiTOS project after the core Rconfig system is established and proven.
