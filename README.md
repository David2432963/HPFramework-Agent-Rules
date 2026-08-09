# HPFramework Agent Rules

This repository contains the rules, guardrails, and knowledge base intended for AI agents operating within Unity projects that utilize the [HPFramework](https://github.com/David2432963/HPFramework.git).

## Overview

The rules and knowledge contained in this repository ensure that AI coding assistants adhere to the strict conventions, architecture, and safety guidelines required when developing with the HPFramework.

### Key Components

- **`AGENTS.md`**: The root rule file for AI agents. It contains high-level rules, hard guardrails, priority orders, and instructions on how to search for knowledge.
- **`Knowledge/`**: A directory containing detailed guidelines across various domains:
  - `CodeQuality.md`: Clean code, naming conventions, class responsibility, and abstraction.
  - `RuntimeReferenceRules.md`: Dependency/reference runtime rules, scene/hierarchy lookup constraints.
  - `UnityAssetSafety.md`: Guidelines for handling prefabs, scenes, ScriptableObject assets, `.meta` files, GUIDs, and the working tree safely.
  - `RuntimeSafetyAndPerformance.md`: Best practices for hot paths, allocation, physics, pooling, logging, and profiling.
  - `DataAndPersistence.md`: Configuration, runtime state, save data, and schema compatibility.
  - `AsyncAndLifecycle.md`: Asynchronous operations, cancellation, initialization, and lifetime ownership.
  - `HPFrameworkReference.md`: Specific reference for the HP Framework, VContainer, UI, pooling, EventBus, and setup.

## Usage

When an AI agent is working on an HPFramework project, it should first consult `AGENTS.md` to understand its boundaries and priorities, and dynamically refer to the specific files in the `Knowledge/` folder as needed to perform tasks without compromising the project's integrity or architecture.
