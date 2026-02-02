# Architecture Overview

## Purpose and high-level shape
The HL7 FHIR IG Publisher builds FHIR Implementation Guides (IGs) from a local IG directory. The entry point for the runnable CLI JAR is `org.hl7.fhir.igtools.publisher.Publisher`, which orchestrates the build process and delegates work to loader, processor, and generator helpers that split the large publisher workflow into focused components. This repository is a multi-module Maven project with a core library module and a CLI packaging module. 【F:README.md†L27-L33】【F:pom.xml†L38-L44】【F:pom.xml†L65-L68】【F:org.hl7.fhir.publisher.cli/pom.xml†L53-L57】【F:org.hl7.fhir.publisher.core/src/main/java/org/hl7/fhir/igtools/publisher/Publisher.java†L122-L151】

## Module layout

### `org.hl7.fhir.publisher.core`
* **Role:** Primary implementation and business logic for IG publishing.
* **Key entry point:** `org.hl7.fhir.igtools.publisher.Publisher` (used by CLI JAR manifests). It owns the orchestrated build lifecycle and provides shared interfaces for reference resolution and validation tracking. 【F:org.hl7.fhir.publisher.cli/pom.xml†L53-L57】【F:org.hl7.fhir.publisher.cli/pom.xml†L82-L87】【F:org.hl7.fhir.publisher.core/src/main/java/org/hl7/fhir/igtools/publisher/Publisher.java†L164-L174】
* **Composition of the main workflow:** The publisher delegates to four primary classes, described directly in the `Publisher` class documentation:
  * `PublisherFields` — shared data/state container.
  * `PublisherBase` — shared utilities for the workflow.
  * `PublisherIGLoader` — load and initialize IG inputs.
  * `PublisherProcessor` — validation and content processing.
  * `PublisherGenerator` — output generation (site, artifacts, etc.). 【F:org.hl7.fhir.publisher.core/src/main/java/org/hl7/fhir/igtools/publisher/Publisher.java†L131-L151】

### `org.hl7.fhir.publisher.cli`
* **Role:** Packaging module that builds the shaded CLI JAR and points the manifest to `org.hl7.fhir.igtools.publisher.Publisher`.
* **Runtime packaging:** Uses the Maven Shade plugin to build a runnable CLI artifact with the core module on the classpath. 【F:org.hl7.fhir.publisher.cli/pom.xml†L43-L47】【F:org.hl7.fhir.publisher.cli/pom.xml†L45-L47】【F:org.hl7.fhir.publisher.cli/pom.xml†L53-L77】

## Runtime flow (high level)
The `Publisher` class documents the overall orchestration sequence that guides the runtime flow. It loads the base FHIR context, connects to terminology services, loads IG content in a prescribed order, validates source files, generates outputs for each resource, and produces summary artifacts. The main publisher then delegates the bulk of this work to the loader/processor/generator components described above. 【F:org.hl7.fhir.publisher.core/src/main/java/org/hl7/fhir/igtools/publisher/Publisher.java†L94-L151】

## Package-level organization (core module)
The core module is organized by functional domains under `org.hl7.fhir.igtools`, with packages for:
* **`publisher` and its subpackages** — core orchestration, loaders, processors, generators, comparators, parsers, realm support, and module-specific tooling.
* **`renderers`** — HTML/IG rendering and output creation.
* **`templates`** — template processing for IG builds.
* **`spreadsheets`** — spreadsheet-based input support.
* **`ui` and `web`** — UI and web-related utilities for the publisher.
* **`logging`** — logging utilities used throughout the build workflow. 【F:org.hl7.fhir.publisher.core/src/main/java/org/hl7/fhir/igtools/publisher/Publisher.java†L39-L88】【F:org.hl7.fhir.publisher.core/src/main/java/org/hl7/fhir/igtools/publisher/Publisher.java†L122-L151】

## Build system and dependencies
The repository is a multi-module Maven project. The root POM defines shared dependency versions and plugin configuration, while module POMs declare module-specific packaging and shading. Java 17 is required for builds. 【F:pom.xml†L4-L12】【F:pom.xml†L26-L41】【F:pom.xml†L58-L68】
